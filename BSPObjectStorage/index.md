---
layout: page
title: BSPObjectStorage
---

Recently I ran into the need to optimise the performance of drawing many tens of thousands of objects. I started to notice that the time to traverse a linear array discovering which objects needed to be drawn (according to NSView's -needsToDrawRect: method) was becoming significant. What I describe here is one possible solution to the problem of discovering objects needing to be drawn from an arbitrarily large set. Other solutions are also well-known, such as RTrees, but I'll leave that for another day.

This solution makes use of a common search algorithm - that of the BinarySearch - in fact given a sorted list of objects, the binary search is the fastest known method to find a given object.

Here, a binary search is extended into 2 dimensions, and the criterion for determining the location of any given object is its bounding rectangle. This is called Binary Search Partitioning, and is a well-known divide-and-conquer method for graphics.

The overall area of a given view is alternately divided and subdivided horizontally and vertically into two partitions, forming a binary tree. The whole view is the root, and the leaves represent some small area of the view. The tree depth determines how small this area is. If the tree depth is 1, there is only one leaf, and the system is equivalent to the linear array case. The number of objects stored at each leaf will usually depend on the depth also - the more finely divided the view, the fewer objects tend to be stored at any given leaf, assuming that on average they are distributed evenly within the view.

The advantage of this approach is that we can quickly return a small set of objects that intersect the view's drawing update area without having to test every one - the input to the tree is a rectangle, and the result is a list of objects that intersect it. NSView has the method -getRectsBeingDrawn:count: which will return a list of rectangles needing update. We can use this as input to our tree.

We might also need to consider the front-to-back ordering (Z-order) of the objects. The tree code here doesn't handle this part, but it's easy enough to do if each object maintains a Z-value - the returned list can simply be sorted according to Z. Naturally the Z-value must be maintained as objects are inserted and deleted into the storage.

The tree code is presented below. To add or remove an object from the tree, a bounds parameter must be supplied which locates the object in 2-dimensional space. Typically an object would supply its own bounds, but in this code that is handled externally. In addition, should an object move, it needs to be repositioned in the tree. This is most easily accomplished by removing it using its old bounds value then adding it back with the new bounds. The tree itself uses recursion to accomplish the tree traversal, and the code here optimises the object-C method call to make that as fast as possible. It also makes use of toll-free bridging to CFArray and friends to speed up other operations. When a complex drawing is being repeatedly redrawn (as when scrolling for example) these optimisations are worth having. An object needs to respond to -setMarked: which is just a flag that the tree uses to avoid including the same object more than once. The objects should have this flag reset before the next search, on eopportunity to do this is when you iterate the returned list to draw the objects.

Hope this is useful to someone - a more complete implementation is found in DrawKit. --GrahamCox.

    

typedef enum
{
	kOperationInsert,
	kOperationDelete,
	kOperationAccumulate
}
BSPOperation;


typedef enum
{
	kNodeHorizontal,
	kNodeVertical,
	kNodeLeaf
}
LeafType;



@interface BSPTree : NSObject
{
@protected
	NSMutableArray*		mLeaves;
	NSMutableArray*		mNodes;
	NSSize				mCanvasSize;
	BSPOperation		        mOp;
@public
	id	                                mObj;
	NSMutableArray*		mFoundObjects;
	unsigned				mObjectCount;
}

- (id)				initWithCanvasSize:(NSSize) size depth:(unsigned) depth;
- (NSSize)			canvasSize;

- (void)			setDepth:(unsigned) depth;
- (unsigned)		countOfLeaves;

- (void)			insertItem:(id) obj withRect:(NSRect) rect;
- (void)			removeItem:(id) obj withRect:(NSRect) rect;
- (void)			removeAllObjects;
- (unsigned)		count;

// tree returns mutable results so that they can be sorted in place without needing to be copied

- (NSMutableArray*)	objectsIntersectingRects:(const NSRect*) rects count:(unsigned) count;
- (NSMutableArray*)	objectsIntersectingRect:(NSRect) rect;
- (NSMutableArray*)	objectsIntersectingPoint:(NSPoint) point;

@end

#pragma mark -

/// node object - only used internally with BSPTree

@interface BSPNode : NSObject
{
@public
	LeafType		mType;
	union
	{
		float		mOffset;
		unsigned	mIndex;
	}u;
}

- (void)			setType:(LeafType) aType;
- (LeafType)		type;
- (void)			setLeafIndex:(unsigned) indx;
- (unsigned)		leafIndex;
- (void)			setOffset:(float) offset;
- (float)			offset;


@end



//=============================================================================================

static inline unsigned childNodeAtIndex( unsigned nodeIndex )
{
	return (nodeIndex << 1) + 1;
}


@implementation BSPTree


- (id)				initWithCanvasSize:(NSSize) size depth:(unsigned) depth
{
	self = [super init];
	if( self )
	{
		mCanvasSize = size;
		mNodes = [[NSMutableArray alloc] init];
		mLeaves = [[NSMutableArray alloc] init];
                mFoundObjects = [[NSMutableArray alloc] init];		

		[self setDepth:depth];
	}
	
	return self;
}


- (NSSize)			canvasSize
{
	return mCanvasSize;
}



- (void)			setDepth:(unsigned) depth
{
	[self removeNodesAndLeaves];

	unsigned i, nodeCount = (( 1 << ( depth + 1)) - 1);
	
	// prefill the nodes array
	
	for( i = 0; i < nodeCount; ++i )
	{
		BSPNode* node = [[BSPNode alloc] init];
		[mNodes addObject:node];
		[node release];
	}
	
	[self allocateLeaves:( 1 << depth )];
	
	NSRect canvasRect = NSZeroRect;
	canvasRect.size = [self canvasSize];
	
	[self partition:canvasRect depth:depth index:0];
}

- (void)			removeNodesAndLeaves
{
	[mNodes removeAllObjects];
	[mLeaves removeAllObjects];
}


- (void)			allocateLeaves:(unsigned) howMany
{
	// prefill the leaves array, which is an array of mutable arrays
	
	unsigned i;
	
	for( i = 0; i < howMany; ++i )
	{
		id leaf = [[NSMutableArray alloc] init];
		[mLeaves addObject:leaf];
		[leaf release];
	}
}


static unsigned sLeafCount = 0;

- (void)			partition:(NSRect) rect depth:(unsigned) depth index:(unsigned) indx
{
	// recursively subdivide the total canvas size into equal halves in alternating horizontal and vertical directions.
	// This is done once when the tree is built or rebuilt.
	
	BSPNode* node = [mNodes objectAtIndex:indx];
	
	if( indx == 0 )
	{
		[node setType:kNodeHorizontal];
		[node setOffset:NSMidX( rect )];
		sLeafCount = 0;
	}
	
	if ( depth > 0 )
	{
		LeafType	type;
		NSRect	ra, rb;
		float		oa, ob;
		
		if([node type] == kNodeHorizontal)
		{
			type = kNodeVertical;
			ra = NSMakeRect( NSMinX( rect ), NSMinY( rect ), NSWidth( rect ), NSHeight( rect) * 0.5f);
			rb = NSMakeRect( NSMinX( rect ), NSMaxY( ra ), NSWidth( rect ), NSHeight( rect ) - NSHeight( ra ));
			oa = NSMidX( ra );
			ob = NSMidX( rb );
		}
		else
		{
			type = kNodeHorizontal;
			ra = NSMakeRect( NSMinX( rect ), NSMinY( rect ), NSWidth( rect ) * 0.5f, NSHeight( rect ));
			rb = NSMakeRect( NSMaxX( ra), NSMinY( rect ), NSWidth( rect ) - NSWidth( ra ), NSHeight( rect ));
			oa = NSMidY( ra );
			ob = NSMidY( rb );
		}
		
                unsigned chIdx = childNodeAtIndex( indx );
		
                BSPNode* child = [mNodes objectAtIndex:chIdx];
		[child setType:type];
		[child setOffset:oa];
		
                child = [mNodes objectAtIndex:chIdx + 1];
                [child setType:type];
		[child setOffset:ob];
		
                [self partition:ra depth:depth - 1 index:chIdx];
                [self partition:rb depth:depth - 1 index:chIdx + 1];
    }
    else
    {
        [node setType:kNodeLeaf];
        [node setLeafIndex:sLeafCount++];
    }
}


// if set to 1, recursive function avoids obj-C message dispatch for slightly more performance

#define qUseImpCaching		1


- (void)			recursivelySearchWithRect:(NSRect) rect index:(unsigned) indx
{
#if qUseImpCaching	
	static void(*sfunc)( id, SEL, NSRect, unsigned ) = nil;
	
	if ( sfunc == nil )
		sfunc = (void(*)( id, SEL, NSRect, unsigned ))self class] instanceMethodForSelector:_cmd];
#endif

    [[BSPNode* node = [mNodes objectAtIndex:indx];
    unsigned subnode = childNodeAtIndex( indx );
	
    switch ( node->mType )
	{
		case kNodeHorizontal:
			if ( NSMinY( rect ) < node->u.mOffset )
			{
				#if qUseImpCaching	
				sfunc( self, _cmd, rect, subnode );
				#else
				[self recursivelySearchWithRect:rect index:subnode];
				#endif
				if( NSMaxY( rect ) >= node->u.mOffset )
					#if qUseImpCaching	
					sfunc( self, _cmd, rect, subnode + 1 );
					#else
					[self recursivelySearchWithRect:rect index:subnode + 1];
					#endif
			}
			else
				#if qUseImpCaching	
				sfunc( self, _cmd, rect, subnode + 1 );
				#else
				[self recursivelySearchWithRect:rect index:subnode + 1];
				#endif
			break;
			
		case kNodeVertical:
			if ( NSMinX( rect ) < node->u.mOffset )
			{
				#if qUseImpCaching	
				sfunc( self, _cmd, rect, subnode );
				#else
				[self recursivelySearchWithRect:rect index:subnode];
				#endif
				if( NSMaxX( rect ) >= node->u.mOffset )
					#if qUseImpCaching	
					sfunc( self, _cmd, rect, subnode + 1 );
					#else
					[self recursivelySearchWithRect:rect index:subnode + 1];
					#endif
			}
			else
				#if qUseImpCaching	
				sfunc( self, _cmd, rect, subnode + 1 );
				#else
				[self recursivelySearchWithRect:rect index:subnode + 1];
				#endif
			break;
			
		case kNodeLeaf:
			[self operateOnLeaf:[mLeaves objectAtIndex:node->u.mIndex]];
			break;
			
		default:
			break;
    }
}



- (void)			recursivelySearchWithPoint:(NSPoint) pt index:(unsigned) indx
{
    BSPNode* node = [mNodes objectAtIndex:indx];
    unsigned subnode = childNodeAtIndex( indx );
	
    switch ([node type])
	{
		case kNodeLeaf:
			[self operateOnLeaf:[mLeaves objectAtIndex:[node leafIndex]]];
			break;
			
		case kNodeVertical:
			if ( pt.x < [node offset])
				[self recursivelySearchWithPoint:pt index:subnode];
			else
				[self recursivelySearchWithPoint:pt index:subnode + 1];
			break;
			
		case kNodeHorizontal:
			if ( pt.y < [node offset])
				[self recursivelySearchWithPoint:pt index:subnode];
			else
				[self recursivelySearchWithPoint:pt index:subnode + 1];
			break;
			
		default:
			break;
    }
}


#define USE_CF_APPLIER         1

static void			addValueToFoundObjects( const void* value, void* context )
{
	id obj = (id)value;
	
	if(![obj isMarked] && [obj visible])
	{
		BSPTree* tree = (BSPTree*)context;
		[obj setMarked:YES];
		CFArrayAppendValue((CFMutableArrayRef)tree->mFoundObjects, value );
	}
}



- (void)			operateOnLeaf:(id) leaf
{
	// <leaf> is a pointer to the NSMutableArray at the leaf
	
	switch( mOp )
	{
		case kOperationInsert:
			[leaf addObject:mObj];
			break;
			
		case kOperationDelete:
			[leaf removeObject:mObj];
			break;
			
		case kOperationAccumulate:
		{
#if USE_CF_APPLIER
			CFArrayApplyFunction((CFArrayRef) leaf, CFRangeMake( 0, [leaf count]), addValueToFoundObjects, self );
#else			
			NSEnumerator* iter = [leaf objectEnumerator];
			id anObject;
			
			while(( anObject = [iter nextObject]))
			{
				if(![anObject isMarked] && [anObject visible])
				{
					[anObject setMarked:YES];
					[mFoundObjects addObject:anObject];
				}
			}
#endif
		}
		break;
			
		default:
			break;
	}
}


- (void)			insertItem:(id<DKStorableObject>) obj withRect:(NSRect) rect
{
    if ([mNodes count] == 0)
        return;
	
	if( obj && !NSIsEmptyRect( rect ))
	{
		mOp = kDKOperationInsert;
		mObj = obj;
		[self recursivelySearchWithRect:rect index:0];
		
		++mObjectCount;
	}
	
	//NSLog(@"inserted obj = %@, bounds = %@", obj, NSStringFromRect( rect ));
}


- (void)			removeItem:(id<DKStorableObject>) obj withRect:(NSRect) rect
{
    if ([mNodes count] == 0)
        return;
	
	if( obj && !NSIsEmptyRect( rect ))
	{
		[obj setMarked:NO];
		mOp = kOperationDelete;
		mObj = obj;
		[self recursivelySearchWithRect:rect index:0];
		
		if( mObjectCount > 0 )
			--mObjectCount;
	}
}


- (void)			removeAllObjects
{
	NSEnumerator* iter = [mLeaves objectEnumerator];
	NSMutableArray*	leaf;
	
	while(( leaf = [iter nextObject]))
		[leaf removeAllObjects];
	
	mObjectCount = 0;
}


- (NSMutableArray*)	objectsIntersectingRects:(const NSRect*) rects count:(unsigned) count
{
	// this may be used in conjunction with NSView's -getRectsBeingDrawn:count: to find those objects that intersect the non-rectangular update region.
	
    if ([mNodes count] == 0)
        return nil;
	
	mOp = kOperationAccumulate;
	[mFoundObjects removeAllObjects];
	
	unsigned i;
	
	for( i = 0; i < count; ++i )
		[self recursivelySearchWithRect:rects[i] index:0];
	
	return mFoundObjects;
}


- (NSMutableArray*)	objectsIntersectingRect:(NSRect) rect
{
    if ([mNodes count] == 0)
        return nil;
	
	mOp = kOperationAccumulate;
	[mFoundObjects removeAllObjects];
	
	[self recursivelySearchWithRect:rect index:0];
	return mFoundObjects;
}


- (NSMutableArray*)	objectsIntersectingPoint:(NSPoint) point
{
    if ([mNodes count] == 0)
        return nil;
	
	mOp = kOperationAccumulate;
	[mFoundObjects removeAllObjects];
	
	[self recursivelySearchWithPoint:point index:0];
	return mFoundObjects;
}


- (unsigned)		count
{
	// returns the number of unique objects in the tree. Note that this value can be unreliable if the client didn't take care (i.e. calling removeItem with a bad object).
	
	return mObjectCount;
}

- (void)			dealloc
{
	[mNodes release];
	[mLeaves release];
        [mFoundObjects release];
	[super dealloc];
}


- (NSString*)		description
{
	// warning: description string can be very large, as it enumerates the leaves
	
	return [NSString stringWithFormat:@"<%@ %p>, %d leaves = %@", NSStringFromClass([self class]), self, [self countOfLeaves], mLeaves];
}


#pragma mark -


@implementation BSPNode

- (void)			setType:(LeafType) aType
{
	mType = aType;
}



- (LeafType)		type
{
	return mType;
}




- (void)			setLeafIndex:(unsigned) indx
{
	u.mIndex = indx;
}



- (unsigned)		leafIndex
{
	return u.mIndex;
}




- (void)			setOffset:(float) offset
{
	u.mOffset = offset;
}



- (float)			offset
{
	return u.mOffset;
}

@end


