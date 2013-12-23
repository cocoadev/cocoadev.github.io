---
layout: page
title: NSLogNotAlwaysUsingDescription
---

I have implemented description as described under DebuggingTechniques, but it seems rather arbitrary wether or not NSLog() uses my implementation, or the default NSObject implementation which just print "<class: address>".

What could possibly be the reason? I have exactly the same NSLog() statements switch between the two implementations (so it would seem) in a running application.

*I seem to have found the problem -- the NSLog() displays the wrong description when something is bound to my object, unfortunately I do not know how to solve it.*

"When something is bound to my object" - can you elaborate?  I've never had any problems with NSLog picking up my description.

----

Try this:
    
@interface Foo : NSObject
{
   NSString* name;
}
- (NSString*)description;
@end

@implementation Foo
- (NSString*)description
{
   return name ?: @"untitled";
}
@end

int main (int argc, char const* argv[])
{
   NSAutoreleasePool* pool = [[NSAutoreleasePool alloc] init];

   Foo* a = Foo alloc] init];
   Foo* b = [[Foo alloc] init];
   [a bind:@"name" toObject:b withKeyPath:@"name" options:nil];
   [[NSLog(@"%@, %@", [a description], [b description]);

   [pool release];
   return 0;
}


In Jaguar, with the 'bind' call commented out, I see descriptions for both.  I don't have my Panzer system handy to try the seccond one.  The naked ?: looks suspicious (rather than name ? name : @"untitled"), though it seems to work.  Maybe it's compiler-depentent behavior that it is working, or it's a new handy C shortcut I've learned today.

*Yes, it works without the bind, hence my statement about not working when something is bound to my object -- ?: is a GCC extension.*

The reason this happens seems to be that the bindings system replaces objects of a given class with objects of a proxy class based upon the original one.  Basically, you are not really working with the class you think you are.  Instead, when you call a method, the proxy object calls your class's implementation of the method in most cases.  Unfortunately, one of the cases in which it doesn't do so seems to be when you call description.  The bindings system is really nice sometimes, but this specific aspect is annoying.

I think they fixed it in Tiger. Or at least they were supposed to :).

