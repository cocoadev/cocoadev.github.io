---
layout: page
title: FSObject
---

FSObject is intended for users of FScript who want to do much more... like say create class hierarchies, data source and other delegate scripts and generally do everything you can do in ObjC but dynamically... I will share my implementation here when I have one but here's the (in progress) interface (annotated)  (UPDATED July 5, 2004) (Updated again July 6, 2004)(solidified (never finalized) July 7,2004)... there is a bug in FScript that makes it have trouble with any selector that starts with a colon.... changed it to my:: instead of just ::... Oh yeah... and it works... here's the implementation too. (Updated July 8,2004) put a seperator in so you can CLEARLY see what parts are interface and what is implementation.... see also discussion at bottom.

----

    
#import <Cocoa/Cocoa.h>
#import <FScript/FScript.h>

@interface FSObject : NSObject
{
	NSMutableDictionary* myProperties;
}
+(FSObject*)object;
-(void)my:(NSString*)message :(id)value;
-(FSObject*)endObject;
@end

@interface Block (FSObjectInheritance)
-(id)object;
@end

----
    
#import "FSObject.h"
#import "/usr/include/objc/objc-class.h"

@interface FSObject (Placeholders)
-(id)_;
-(id)_:(id)a;
-(id)_:(id)a :(id)b;
-(id)_:(id)a :(id)b :(id)c;
-(id)_:(id)a :(id)b :(id)c :(id)d;
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e;
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e :(id)f;
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e :(id)f :(id)g;
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e :(id)f :(id)g :(id)h;
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e :(id)f :(id)g :(id)h :(id)i;
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e :(id)f :(id)g :(id)h :(id)i :(id)j;
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e :(id)f :(id)g :(id)h :(id)i :(id)j :(id)k;
@end

@implementation FSObject (PlaceHolders)
-(id)_
{
	NSString* message = NSStringFromSelector(_cmd);
	id myObject;
	myObject = [myProperties objectForKey:message];
	if( [myObject respondsToSelector:@selector(value:)] ) {
		return [myObject value:self];
	} else {
		return myObject;
	}
	return nil;		
}

-(id)_:(id)a
{
	NSString* message = NSStringFromSelector(_cmd);
	id myObject;
	myObject = [myProperties objectForKey:message];
	return [myObject value:self value:a];
}
-(id)_:(id)a :(id)b
{
	NSString* message = NSStringFromSelector(_cmd);
	id myObject;
	myObject = [myProperties objectForKey:message];
	return [myObject value:self value:a value:b];
}
-(id)_:(id)a :(id)b :(id)c
{
	NSString* message = NSStringFromSelector(_cmd);
	id myObject;
	myObject = [myProperties objectForKey:message];
	return [myObject value:self value:a value:b value:c];		
}
-(id)_:(id)a :(id)b :(id)c :(id)d
{
	NSString* message = NSStringFromSelector(_cmd);
	id myObject;
	myObject = [myProperties objectForKey:message];
	return [myObject value:self value:a value:b value:c value:d];	
	
}
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e
{
	NSString* message = NSStringFromSelector(_cmd);
	id myObject;
	myObject = [myProperties objectForKey:message];
	return [myObject value:self value:a value:b value:c value:d value:e];	
	
}
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e :(id)f
{
	NSString* message = NSStringFromSelector(_cmd);
	id myObject;
	myObject = [myProperties objectForKey:message];
	return [myObject value:self value:a value:b value:c value:d value:e value:f];		
}
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e :(id)f :(id)g
{
	NSString* message = NSStringFromSelector(_cmd);
	id myObject;
	myObject = [myProperties objectForKey:message];
	return [myObject value:self value:a value:b value:c value:d value:e value:f value:g];		
}
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e :(id)f :(id)g :(id)h
{
	NSString* message = NSStringFromSelector(_cmd);
	id myObject;
	myObject = [myProperties objectForKey:message];
	return [myObject value:self value:a value:b value:c value:d value:e value:f value:g value:h];		
}
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e :(id)f :(id)g :(id)h :(id)i
{
	NSString* message = NSStringFromSelector(_cmd);
	id myObject;
	myObject = [myProperties objectForKey:message];
	return [myObject value:self value:a value:b value:c value:d value:e value:f value:g value:h value:i];		
}
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e :(id)f :(id)g :(id)h :(id)i :(id)j
{
	NSString* message = NSStringFromSelector(_cmd);
	id myObject;
	myObject = [myProperties objectForKey:message];
	return [myObject value:self value:a value:b value:c value:d value:e value:f value:g value:h value:i value:j];		
}
-(id)_:(id)a :(id)b :(id)c :(id)d :(id)e :(id)f :(id)g :(id)h :(id)i :(id)j :(id)k
{
	NSString* message = NSStringFromSelector(_cmd);
	id myObject;
	myObject = [myProperties objectForKey:message];
	return [myObject value:self value:a value:b value:c value:d value:e value:f value:g value:h value:i value:j value:k];	
}
@end

@implementation FSObject 
+(FSObject*)object
{
	return [[[FSObject alloc] init] autorelease];
}

-(id)init
{
	self = [super init];
	myProperties = [NSMutableDictionary dictionary];
	[myProperties retain];
	return self;
}

-(void)dealloc
{
	[myProperties release];
	[super dealloc];
}

-(void)my:(NSString*)message :(id)value
{	
	[myProperties setObject:value forKey:message];
	SEL theSelector = NSSelectorFromString(message);
	if(! theSelector) {
		theSelector = sel_registerName([message cString]);
	}
	if(! [self respondsToSelector:theSelector]) {
		struct objc_method * oldMethod;
		int colonCount = message componentsSeparatedByString:@":"] count] - 1;
		switch(colonCount) {
			case 0:
				oldMethod = class_getInstanceMethod([self class], @selector(_)); 
				break;
			case 1:
				oldMethod = class_getInstanceMethod([self class], @selector(_:));
				break;
			case 2:
				oldMethod = class_getInstanceMethod([self class], @selector(_::));
				break;
			case 3:
				oldMethod = class_getInstanceMethod([self class], @selector(_:::));
				break;
			case 4:
				oldMethod = class_getInstanceMethod([self class], @selector(_::::));
				break;
			case 5:
				oldMethod = class_getInstanceMethod([self class], @selector(_:::::));
				break;
			case 6:
				oldMethod = class_getInstanceMethod([self class], @selector(_::::::));
				break;
			case 7:
				oldMethod = class_getInstanceMethod([self class], @selector(_:::::::));
				break;
			case 8:
				oldMethod = class_getInstanceMethod([self class], @selector(_::::::::));
				break;
			case 9:
				oldMethod = class_getInstanceMethod([self class], @selector(_:::::::::));
				break;
			case 10:
				oldMethod = class_getInstanceMethod([self class], @selector(_::::::::::));
				break;
			case 11:
				oldMethod = class_getInstanceMethod([self class], @selector(_:::::::::::));
				break;
		}
				
		struct objc_method_list * newCategory = malloc(sizeof(struct objc_method_list));
		newCategory->method_count = 1;
		memset(&newCategory->method_list[0], 0, sizeof(struct objc_method));

		newCategory->method_list[0].method_name = theSelector;
		newCategory->method_list[0].method_types = oldMethod->method_types;
		newCategory->method_list[0].method_imp = oldMethod->method_imp;
		
		
		class_addMethods([self class], newCategory);
	}	
}

-([[FSObject*)endObject
{
	return self;
}
@end

@implementation Block (FSObjectInheritance)
-(id)object
{
	return [self value];
}
@end


here's some example FScript code that uses this interface

    
MyDataSource := [(FSObject object)

	my: 'numberOfItemsInTableView:' : 
		[ :self :tv |
			(self myData) count.
		];

	my: 'tableView:objectValueForTableColumn:row:' : 
		[ :self :tv :tc :tr |
			(((self myData) objectAtIndex:tr) objectForKey:(tc identifier)).
		];

	my: 'tableView:setObjectValue:forTableColumn:row:' : 
		[ :self :tv :val :tc :tr |
			(((self myData) objectAtIndex:tr) setObject:val forKey:(tc identifier)).
		];

	my: 'newRecord' : 
		[ :self |
			newRecord := NSMutableDictionary alloc init;
				setObject:'default' forKey:'someKey'.
				"some other defaults..."
			self myData addObject:newRecord.
			nil.
		];

	my: 'myData' : (NSMutableArray alloc init);

endObject.].

newDataSource := (MyDataSource object).
aTableView setDataSource:newDataSource.
aMenuItem setTarget:newDataSource; setAction:#newRecord.


What do you think... I'm thinking about IB Live mode myself...

----

Yeeeeees! Prototype-based inheritance rocks. --TheoHultberg/Iconara

----

Re: interface update on July 6, 2004... Basically made it more like JavaScript, which simplifies the implementation quite a bit... You create a class's constructor as a Block... which calls a bunch of methods on an instance of the class it inherits from, the root of them all being FSObject...  here's a simple vehicle to car heirarchy in the new interface:

    

Vehicle := [(FSObject object)

	my: 'accelerate' : [ :self |
		self  my: 'velocity' : (self velocity + (self acceleration)).
	nil.];

	my: 'velocitiy' : 0; "start at a stop"

	my: 'acceleration' : 0; "a generic vehicle accelerates at what speed?"

	"other methods/members..."

endObject.].

Car := [(Vehicle object)

	my: 'acceleration' : 20; "over-ride an inherited value"

	my: 'wheels' : 4; "add new properties"

	"other things..."

endObject.].

myCar := Car object. "instantiate a new Car"
myCar accelerate. "calling the inherited method..."
myCar velocity. "now 20..."


----

It's a nice thing, and prototype-based inheritance is really cool and all, but there is one thing that I just have to comment on: the     Placeholders category on     FSObject. It feels like bad design to limit the number of parameters possible, or even to hard-code into the implementation which number of parameters possible, you get two ugly things: the category mentioned, which is redundant code all over the place, and the switch/case in     my::, which also feels like a thing to avoid if possible. I imagine that there is no easy way of doing this, and that's the reason why you have resolved to this implementation (a "fulhack" as we would say in Swedish). It would be nice to hear your thoughts (and frustrations, I guess) on this issue.

--TheoHultberg/Iconara

----

well I am not sure if I know a way around this limit... The reason I layed it out like this is because the FScript Block object has methods defined from value with no arguments to value:value:...value:value: with twelve arguments... I allowed for blocks which use 0...11 arguments (the first one is added in automatically by me as "self"....  This semi-arbitrary decision is actually not from me but from the writer of FScript.  After having done the runtime hacking to get this to work, I can see the challenges that would face someone who wanted to automatically have the object respond to any number of arguments... The FScript solution is to accept a single argument, an NSArray in a special version of the call... I could play to make that work, but this was my first attempt, and it worked!... It may however be possible to create an object that responds to any selector, NSProxy objects do this... I don't know how, but they pretend to respond to any arbitrary selector, then they ask the remote object if they do or else they consult the protocol you tell them to conform to... VERY STRANGE, I'd like to know how it works, (the protocol bit probably generates at runtime a ClassCluster, which is one thought I had for FScript Blocks but became unnecessary after doing this).

To sum up, the runtime says in most cases all messages you want to respond to you have to add one at a time literally to the class definition.  NSProxy gets around this somehow, so maybe I could base FSObject off of NSProxy instead of NSObject.... but I don't know enough about how NSProxy does what it does to subclass it to take advantage of this feature.  The ugly category for placeholders has been put into the implementation file, so if it was in a framework you would never see that mess. -- MooreSan

PS.  I really dislike switch statements and code repetition... I consider it a sign of poor design every time... except this time.... it was FAR easier... in fact, I can't think of a better way to do it... if there's any suggestions I am completely open.

----

I peeked through the docs for NSProxy, and it appears that my runtime hacking and all was for naught... If FSObject inherits from NSProxy instead of NSObject, all I have to do is implement two functions (whose implementations for this are nearly trivial) and it all works.... updated implementation coming soon... MooreSan

Nix that.. subclassing NSProxy is a major headache... I thought they would be trivial, but it turn out the interfaces for the classes I need to manipulate in those methods are not coder-friendly... The first one gives me an NSInvocation, which gives me no nice way to access its arguments safely (I have to just guess that they are objects... and when they're not its gonna crash...), and in the second I have to return an NSMethodSignature, which has no constructor listed. 

(although the FScriptBrowser tells me it has a +signatureWithObjCTypes: method which I can pass a cString from an NSString like @"@:@@@" for -value:value:value:... however the method isn't listed in the documentation or the interface file so XCode gives me a warning everytime I compile, which means I have to close and open the project... XCodeBugs...grr)

Oh and after I work around that I also have to do some work to battle recursion and to check perform the real functions I want to call on the class...  anyways here's what I have up till now:

    
#import <Cocoa/Cocoa.h>
#import <FScript/FScript.h>

@interface FSObject2 : NSProxy
{
	NSMutableDictionary* myProperties;
}
+(FSObject2*)object;
-(void)my:(NSString*)message :(id)value;
-(FSObject2*)endObject;

-(void)forwardInvocation:(NSInvocation*)anInvocation;
-(NSMethodSignature*)methodSignatureForSelector:(SEL)aSelector;
@end


----
    
#import "FSObject2.h"

@implementation FSObject2
+(FSObject2*)object
{
	return [FSObject2 alloc] init] autorelease];
}

-(void)my:([[NSString*)message :(id)value
{
	[myProperties setObject:value forKey:message];

	SEL theSelector = NSSelectorFromString(message);
	if(! theSelector) {
		theSelector = sel_registerName([message cString]);
	}

	return;
}

-(FSObject2*)endObject
{
	return self;
}

-(void)forwardInvocation:(NSInvocation*)anInvocation
{
	SEL theSelector = [anInvocation selector];
	NSString* theMessage = NSStringFromSelector(theSelector);
	id value = [myProperties objectForKey:theMessage];
	if([value respondsToSelector:@selector(value:)]) {
		int argCount = theMessage componentsSeparatedByString:@":"] count]-1;
		[[NSMutableArray* args = [NSMutableArray arrayWithObject:self];
		int index;
		id  curArg;
		for(index = 0; index < argCount; index++) {
			[anInvocation getArgument:&curArg atIndex:index]; //this fails if it's not an   object...
			[args addObject:curArg];
		}
		return [value withArguments:args];
	}
	return value;
}

-(NSMethodSignature*)methodSignatureForSelector:(SEL)aSelector
{
	NSString* theMessage = NSStringFromSelector(aSelector);
	int argCount = theMessage componentsSeparatedByString:@":"] count]-1;
	[[NSMutableString* signature = [NSMutableString stringWithString:@"@:"];
	int index;
	for(index = 0; index < argCount; index++) {
		[signature appendString:@"@"];
	} // the method used below is not documented API
	return [NSMethodSignature signatureWithObjCTypes:[signature cString]];
}

@end



on attempt to use the code:

    
> FSObject2 object

error: can't invoke method object


----

About "error: can't invoke method object". This comes from a bug in Cocoa (the meta class level of NSProxy is completely broken). There is a built-in workaround in F-Script but it is only partial. To work around this problem, add this method to FSObject2:

    
+ (NSMethodSignature *)methodSignatureForSelector:(SEL)selector
{  
  if      (selector ==  @selector(object)) return [NSMethodSignature signatureWithObjCTypes:"@@:"];
  else return [super methodSignatureForSelector:(SEL)selector];
}

 
BTW, for your code to work you will also need to add an init method to the FSObject2 class. Something like this:

    
- init
{
  myProperties = [[NSMutableDictionary alloc] init];
  return self;
}


The perspective of having an object definition facility for F-Script is pretty exciting. Not sure the NSProxy based approach will work well enough (NSProxy is a bit of a hack... never completely transparent), but we will see.

Philippe Mougin

Well the NSProxy bit is cleaner, but the first set of code right now actually works right now... I have used it to make working objects, classes, etc.  Plan on submitting the code (and some other bits) as soon as the automatic list manager for FScript gets me an email... by the way, it's an honour to have you reply to this topic.  MooreSan

