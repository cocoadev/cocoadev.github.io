---
layout: page
title: CyclicClasses
---

In a stripped down version of my issue, I have an interface with a pair of classes that need to talk to each other: ClassA and ClassB.

    
#import <Cocoa/Cocoa.h>
#import "ClassB.h"

@interface ClassA : NSObject
{
    IBOutlet ClassB *classB;
}
@end


#import <Cocoa/Cocoa.h>
#import "ClassA.h"

@interface ClassB : NSObject
{
    IBOutlet ClassA *classA;
}
@end


I've linked their instances together in interface builder, but when I compile I get a "parse error before ClassA". I'm pretty sure this is because they're importing each other, but using the "#ifndef __CLASS_A__" trick I'm used to didn't work.

Help!
----
See AvoidingBidirectionalOutlets. *Note: that page was orphaned because the OP there assumed it was a unique mistake. Obviously it's not. Any ideas for the long-term location of this information?*

----
I'd say: move it to a page with a good name first, either CyclicClassDependencies or CyclicHeaderDependencies.

----
Also, check out the @class compiler directive.

----

The previous response is correct; use @class for a forward declaration of your classes in your headers.

The rule of thumb is that you should only import headers in your *header* file that are required for your class to compile: The header for your base class, the headers for any protocols your class itself declares that it implements, and the headers for any types that are used in the declarations in your header file that can't be used with just forward declarations (such as typedefs of primitive types).  You can substitute an umbrella header for the above if you wish, if your base class and declarations all come from it.

Thus your declarations would best look like this:

    

#import <Cocoa/Cocoa.h>

@class ClassB;

@interface ClassA : NSObject
{
    IBOutlet ClassB *classB;
}
@end


#import <Cocoa/Cocoa.h>

@class ClassA;

@interface ClassB : NSObject
{
    IBOutlet ClassA *classA;
}
@end



In your implementation files for these classes you would actually import the header files for ClassA and ClassB.

