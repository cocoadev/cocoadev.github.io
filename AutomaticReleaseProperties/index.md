---
layout: page
title: AutomaticReleaseProperties
---

Hi all.

I'm not interested in using GC at the moment but I really like properties. Using them frees you from all that mucky setter/getter coding. Nice.
However, when an object is dealloc'ed you still need to do the work of setting all those properties to nil to release the memory ([self setFoo:nil]). 

I was thinking of automating this process by iterating over all my properties and just releasing them using the obj runtime. Like so:

    

// ReleaseProperties.c

#import <objc/runtime.h>
#import "ReleaseProperties.h"

#define	LOG_DEBUG	0

void	ReleaseInstancePropertiesForNSObject( NSObject* obj, Class objClass )
{
	unsigned int propCount = 0;
	
	objc_property_t *props = class_copyPropertyList( objClass, &propCount );
	if ( props )
	{
		unsigned int	index = 0;
			
		while( index < propCount )
		{
			const char*	propName = property_getName( props[index] );
			if ( propName )
			{
				void*	ivarValue = nil;
				Ivar ivar = object_getInstanceVariable( obj, propName, &ivarValue );
				if ( ivar )
				{
					id	iobj = object_getIvar( obj, ivar );
					if ( iobj ) {
						#if LOG_DEBUG
							NSLog(@"Release property: %s %p from %@", propName, iobj, objClass );
						#endif
						[obj setValue:nil forKey:[NSString stringWithUTF8String:propName]];
					}
				}
			}
			
			++index;
		}
	
		free( props );
	}
}



Combined with a macro like below we can make it really easy to implement a dealloc which simply releases all the properties:

    

// ReleaseProperties.h

/** Note: Properties in your object must be retained!!! Non-retained (assigned) objects will result in over-released objects. **/

void	ReleaseInstancePropertiesForNSObject( NSObject* obj, Class objClass );

#define	DEALLOC_RELEASE_PROPERTIES(a)	-(void)dealloc { ReleaseInstancePropertiesForNSObject( self, [a class] );  [super dealloc];  }
// Usage: DEALLOC_RELEASE_PROPERTIES(YourClassName)



Include ReleaseProperties.h in your source and put DEALLOC_RELEASE_PROPERTIES(YourClassName) in your implementation and your done! 

Now, the big question: Is this a bad idea? Can anybody think of any reasons why this could screw up badly? -- rjt

----
If you've got a circular reference (like drawers and windows, or other closely-related child/parent relationships where references are maintained between two objects), then one object is considered the "parent" and retains the child, but the child does not retain the parent.  Using your function in such a situation will cause an over-release of the parent object.  --K

----

Good point. Using this with non-retained properties would be a very bad idea. Thanks for the feedback. 

----
Once you have the property name you can go straight to KVC and use     setValue:forKey: to clear the property. This will save you from the above and may be better for other situations as well. It will also have the effect of zeroing out your dynamic properties, which could be good or bad depending on the situation.

----

Ok, I've updated the code to use [setValue:forKey:]. However, I'm still using object_getInstanceVariable and object_getIvar to check to see if the property is an object since setValue:forKey: will throw for scalars, structs, etc. 
(BTW, I looked at using valueForKey: but it throws for non-objects also.)

----

I still don't like this idea, because it makes subclassing a dangerous operation.      ReleaseInstancePropertiesForNSObject() causes the superclass to access any properties of its derived classes.  Now, I understand that in a dynamic language like ObjC it's common for the superclass to access behavior that is overridden by a subclass, but here you're manipulating the actual data structure of the subclass.  What if the subclass performs some super-special processing in     -dealloc?  As a completely contrived example, this will break:

    
extern NSMutableSet *freeLater; // A set of objects that we will free later

@interface ClassA : NSObject
{
    @property NSString *label;
}

@end

@implementation ClassA

- (void)dealloc
{
    ReleaseInstancePropertiesForNSObject(self);
    [super dealloc];

    // As far as the author of ClassA is concerned, this should only
    // wind up freeing self.label.
}

@end

// Someone else has come along and created ClassB, which derives
// from ClassA.  It might be another person on the team, maintaining
// the program three years from now.  Who knows?

@interface ClassB : ClassA
{
    @property LongTermObject *lto;
}

@end

@implementation ClassB

- (void)dealloc
{
    // For some reason, LongTermObjects need to persist for a while
    // after their "containing" ClassB objects are destroyed.  They're put on
    // the freeLater list and dealt with at another time.

    // The author of ClassB doesn't know about the shortcut that the author
    // of ClassA took when writing -dealloc, so the following code will fail,
    // despite the intentional lack of a call to [self setLto:nil].

    LongTermObject *myLto = self.lto;
    [super dealloc];
    [freeLater addObject:myLto];  // crashes with message to garbage object
}



In short, you're introducing behavior which is not expected by anyone who subclasses your class.  This is a maintenance nightmare.  --K

----

I think you've broken the golden rule that "dealloc should do no useful work". :-)

But the point is valid - and fixable. See the latest code changes above. Now, when you use the macro you specify the class which needs to have its properties free'd.

Thanks again for the feedback. Please keep it coming.

----

Again, as I said, it's a purely contrived example.  I think the fact that the superclass is mutating properties of any and all subclasses is a far more problematic violation than my trickery within     -dealloc.  Maybe it would have been more poignant if I had done my trickery in a KVO mutator?  What if     -setFoo: relies on the property     bar to be non-nil?  What about read only properties?  --K

(OK, I've seen that you've gotten around the inheritance violation.  But that still doesn't address read-only properties, or in fact any situation in which the property mutator is not synthesized.)

----

setValue:forKey: works fine for non-synthesized and read-only properties. It works at the ivar level if needed. 

As far a "trickery" goes - yes, you can break anything. I'm happy with support for common/standard patterns. 
Maybe it is worth going back to directly releasing the ivar (without using setValue:forKey:). That would help work around your trickery. :-)


Keep that feedback coming!  I want to keep exploring this idea and looking for its limits. 

----
I'm all for exploring a concept, but at the end of the day I have to wonder why you don't just turn on garbage collection and be done with it? In a language like Objective-C where so much is done by convention rather than being enforced by the compiler, it's really helpful to stick with the convention unless there's a compelling reason for doing otherwise. It seems like you're adding significant complexity here to avoid a fairly small bit of work (setting properties to nil), or to avoid using the system that's designed to solve the very problem you're addressing, and more. The complexity comes from the fact that you have to understand what this ReleaseProperties thing does, you have to remember when you can and when you can't use it, and you have to explain it to anyone else that might ever look at your code. -CS

----

I didn't mean that it would break simply because you didn't synthesize the mutator -- though that is, in fact, what I wrote.  What I meant was that there are plenty of legitimate instances where the mutator does something more than simply change an ivar.  You can't go back to simply releasing the ivar, because the existence of an ivar means absolutely nothing as to the retain/release relationship between     self and the object at the other end of the ivar.

Quite frankly from a software engineering standpoint, your comfort with "support for common/standard patterns" is nowhere near sufficient to justify the danger *your* new pattern poses.  Because somebody *will* subclass a class that uses your pattern, in a way that will break.  It might even be you. --K

----

CS, K - I couldn't agree more. I started this idea and I'm far from convinced it is a good idea! But that is really why I posted the code....to get a better understanding of its limits and maybe get a workable/maintainable solution out of it. If those two criteria can't be met there is no way I'd use it in a "real" project.  

I'm just not ready to go that extra step and turn on Apple's garbage collector (mostly for reasons I won't go into here) and wanted a way to avoid writing that extra dealloc code (especially since my dealloc will not even be called when I DO enable GC). -- rjt

----
In that case, consider writing an AppleScript, Xcode plugin, service, or just a command line tool that parses a class interface and emits a     -dealloc with the relevant     [self setFoo:nil] calls. You can then simply paste that into your implementation and modify it as necessary. Perfectly conventional code will be right there where everyone expects it, doing just what any Obj-C programmer would expect, and yet you avoid the tedium of writing it. -CS

----

    

#define	DEALLOC_RELEASE_IVARS(b)	-(void)dealloc 
{ 
	NSString	*ivarsStringNoSpaces = [@b stringByReplacingOccurrencesOfString:@" " withString:@""]; 
	NSArray		*ivarsArray = [ivarsStringNoSpaces componentsSeparatedByString:@","]; 
	for (NSString *ivarName in ivarsArray) { 
		[self setValue:nil forKey:ivarName]; 
	} 

	[super dealloc]; 
}



You'll need to put '\' on the end of each line to make the macro compile.

Usage: DEALLOC_RELEASE_IVARS("myString, myArray")

Do I win a prize for the slowest dealloc ever?

At-least this version puts the onus on the caller to correctly list which ivars they want released.

