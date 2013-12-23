---
layout: page
title: SavingNSSplitViewPosition
---

Here's some simple code to save and restore the position of an NSSplitView (which unfortunately don't save their position). You can call these from your window controller's awakeFromNib/windowDidLoad and termination code.

    @interface NSSplitView (Defaults)
    
    - (void) saveLayoutWithName: (NSString *) defaultName;
    - (void) loadLayoutWithName: (NSString *) defaultName;
    
    @end
    
    @implementation NSSplitView (Defaults)
    
    - (void) saveLayoutWithName: (NSString *) defaultName
    {
        NSMutableArray *rectstrs = [NSMutableArray array];
        for (NSView *view in self.subviews)
            [rectstrs addObject: NSStringFromRect(view.frame)];
        [[NSUserDefaults standardUserDefaults] setObject: rectstrs forKey: defaultName];
    }
    
    - (void) loadLayoutWithName: (NSString *) defaultName
    {
        NSMutableArray *rectstrs = [[NSUserDefaults standardUserDefaults] objectForKey: defaultName];
        if (rectstrs == nil) return;
    
        for (NSView *view in self.subviews) {
            view.frame = NSRectFromString([rectstrs objectAtIndex:0]);
            [rectstrs removeObjectAtIndex:0];
        }
        
        [self adjustSubviews];
    }
    
    @end

----

Too much work to change, easier to rewrite. This also handles collapsed views:

*This only sort of handles collapsed views.  "Collapsed" state is different from just zero height (or width).  In a freshly loaded layout with this code, no subview will report YES for -[splitView isSubviewCollapsed:subview].*

    @interface NSSplitView (CCDLayoutAdditions)
    
    - (void)storeLayoutWithName: (NSString*)name;
    - (void)loadLayoutWithName: (NSString*)name;
    
    @end    
    
    @implementation NSSplitView (CCDLayoutAdditions)
    
    - (NSString*)ccd__keyForLayoutName: (NSString*)name
    {
    	return [NSString stringWithFormat: @"CCDNSSplitView Layout %@", name];
    }
    
    - (void)storeLayoutWithName: (NSString*)name
    {
    	NSString*		key = [self ccd__keyForLayoutName: name];
    	NSMutableArray*	viewRects = [NSMutableArray array];
    	NSEnumerator*	viewEnum = self subviews] objectEnumerator];
    	[[NSView*			view;
    	NSRect			frame;
    	
    	while( (view = [viewEnum nextObject]) != nil )
    	{
    		if( [self isSubviewCollapsed: view] )
    			frame = NSZeroRect;
    		else
    			frame = [view frame];
    			
    		[viewRects addObject: NSStringFromRect( frame )];
    	}
    	
    	[[NSUserDefaults standardUserDefaults] setObject: viewRects forKey: key];
    }
    
    - (void)loadLayoutWithName: (NSString*)name
    {
    	NSString*		key = [self ccd__keyForLayoutName: name];
    	NSMutableArray*	viewRects = [[NSUserDefaults standardUserDefaults] objectForKey: key];
    	NSArray*		views = [self subviews];
    	int				i, count;
    	NSRect			frame;
    		
    	count = MIN( [viewRects count], [views count] );
    	
    	for( i = 0; i < count; i++ )
    	{
    		frame = NSRectFromString( [viewRects objectAtIndex: i] );
    		if( NSIsEmptyRect( frame ) )
    		{
    			frame = views objectAtIndex: i] frame];
    			if( [self isVertical] )
    				frame.size.width = 0;
    			else
    				frame.size.height = 0;
    		}
    			
    		[[views objectAtIndex: i] setFrame: frame];
    	}
    }
    
    @end

----

I've got code similar to ones above that set the position of a split view. Now this works fine for a normal split view, but for a split view inside another split view, this doesn't work. Can anyone see why it might not work? I've tried using     setNeedsDisplay: and     adjustSubviews but neither worked. Thanks.

    - (void)setPositionUsingAutosaveName:([[NSString *)name
    {
    	NSArray *s = [[NSUserDefaults standardUserDefaults] objectForKey:name];;
    	if (s == nil || [s isKindOfClass:[NSArray class]] == NO)
    		return;
    	
    	NSArray *subviews = [self subviews];
    	int i;
    	for (i=0; i<[subviews count] && i<[s count]; i++)
    	{
    		NSRect newRect = NSRectFromString([s objectAtIndex:i]);
    		[[subviews objectAtIndex:i] setFrame:newRect];
    	}
    }

