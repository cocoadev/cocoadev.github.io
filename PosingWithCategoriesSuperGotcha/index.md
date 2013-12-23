---
layout: page
title: PosingWithCategoriesSuperGotcha
---

That's a gotcha with calls to super, not a really special gotcha.  To read up on class posing and categories see 
ExtendingClasses.

Summary:  **Messages to super have the same effect as messages to self in a category on a posing subclass.**

I just ran into something that surprised me.  Suppose you have a class 
    MyClass and a subclass     MyClassPoser that poses for it.  Now suppose
you have a category     MyClassPoser (Private) containing the following method:

    
- (id)valueForKey:(id)aKey
{
	[super valueForKey:aKey];
}


What do you think would happen if you called the method after posing?

    

id object = [[MyClass alloc] init];
	
id dum = [object valueForKey:@"dummy"];


I'd expect that you'd get the method defined by     -[MyClassPoser(Private) valueForKey:], 
which would immediately call through to the method originally named by
    -[MyClass valueForKey:], which would either look up the key dummy, or throw 
an exception or something.  
This is what happens if we move     MyClassPoser's     valueForKey: method
from the category to the main implementation.


As it stands we're going to get an infinite recursion and blow the stack.  It turns
out that

    
- (id)valueForKey:(id)aKey
{
	[super valueForKey:aKey];
}


ends up acting like

    
- (id)valueForKey:(id)aKey
{
	[[MyClass class] instanceMethodForSelector:@selector(valueForKey:)](self, @selector(valueForKey:), aKey);
}


After posing, that's just the same as

    
- (id)valueForKey:(id)aKey
{
	[self valueForKey:aKey];
}


so we go into a tailspin.  I guess this is a bug?  Anyway, I'm submitting it to Apple.

-KenFerry


----


Hi Ken.  Thanks for putting a note up.  Here's a test case you can include with your bug report.  Open Xcode and create a "Foundation Tool" project.  Copy and paste the code below into the file where main is defined.  Build and Run.

    

#import <Foundation/Foundation.h>

@interface MyClass : NSObject {}
-(void)foo;
@end

@implementation MyClass
-(void)foo
{
    static unsigned int call = 1;
    if (call < 3) {
	NSLog(@"-[MyClass foo]\n");
	call++;
    } else {
	assert(0);
    }
}
@end

@interface MyClassPoser : MyClass {}
@end

@implementation MyClassPoser
@end

@interface MyClassPoser (Category)
-(void)foo;
@end

@implementation MyClassPoser (Category)
-(void)foo
{
    static unsigned int call = 1;
    if (call < 3) {
	NSLog(@"-[MyClassPoser foo]\n");
	call++;
	[super foo];
    } else {
	assert(0);
    }
}
@end

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
		
    [MyClassPoser poseAsClass: [MyClass class]];
    id object = [[MyClass alloc] init];
    [object foo];
	
    [object release];
    [pool release];
    return 0;
}



Best, jd

----

Thanks for the code jd.  I already included a test foundation project with my original submission, so it would probably be overkill to include more code (though your code is a little better than mine - mine doesn't log and crashes instead of failing an assertion).  

I think your test makes this page easier to understand though.

-KenFerry

