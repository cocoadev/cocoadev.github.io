---
layout: page
title: ComplicatedSubClassing
---



I'm working on the architecture of a game engine. (I'm essentially gluing a bunch of open-source components together, with an objective-c interface), and I've run into a problem with the design.

Say I have a Class 'A' and it has two subclasses 'ASub1' and 'ASub2'. Both subclasses override a method "Foo" in 'A', with different implimentations. Now imagine that I have a class 'B' which will add some funcionality to the subclasses of 'A.' However, it will sometimes need to use the method "Foo" from 'ASub1' and sometimes the method "Foo" from 'ASub2.'

Obviously, I can just make two classes 'B1' and 'B2' which subclass 'ASub1' and 'ASub2" respectively. But that would duplicate a lot of code, and isn't that what OO is supposed to avoid? Is there any way that I can do this, but with only one 'B' class? I'm not acting for multiple inheritance, but I guess dynamic inheritance! 

-natmartin

----
Objective-C is very nicely dynamic in this regard.  I suspect from your description that you want plain old ordinary polymorphism.  

----

Thanks for your response. However, looking at the code you've posted, it doesn't seem that 'B' would act as a true subclass to either of the As. If 'ASub1' had other methods (foo1, foo2, etc), I couldn't simply call [Bobject foo2] without specifically wrapping each method in a doSomething type method?

Though, now that I think about it, I suppose there is probably someway with method forwarding, etc. I'll look into that.

I'll give a better example, without generic names. I've got a Class called GameObject. This creates new static objects in the world, handles loading meshes, etc, and placing it in the world. It is subclassed by MovableGameObject which handles (as it sounds) moving objects around the scene. MovableGameObject is subclassed by PhysicalGameObject, which is objects that are handled by the physics engine. It uses some of the methods in MovableGameObject, but it overrides some as well.

Now, I want to have Light and Camera objects. These objects will handle any specifics for lights, cameras, etc. I can make them subclasses of MovableGameObject, and I can move them around the scene. But, what if I want the camera to be affected by the physics engine? 

I could subclass Camera and make PhysicalCamera, but I'm duplicating the physics related code. I could subclass PhysicalGameObject and make PhysicalCamera, but now I'm duplicating the camera related code. 

I'd like to have one Camera class, and be able to dynamically chose when I make an object if it subclasses PhysicalGameObject or MovableGameObject. Is this possible?

-natmartin

----
    
@interface A : NSObject
{
}

- (void)foo;

@end


@interface ASub1 : A
{
}

- (void)foo;

@end


@interface ASub2 : A
{
}

- (void)foo;

@end


@interface B : NSObject
// This is a helper class for instances of A
{
   A        *theInstanceOfAToHelp;
}

- (void)setInstanceToHelp:(A *)anA;
- (void)doSomethingWithA;

@end

@implementation B

-(void)dealloc
{
   [self setInstanceToHelp:nil];
   [super dealloc];
}


- (void)setInstanceToHelp:(A *)anA
{  // anA can be any kind of A including an instance of some subclass of A
   [anA retain];
   [theInstanceOfAToHelp release];
   theInstanceOfAToHelp = anA;
}


- (void)doSomethingWithA
{
   [theInstanceOfAToHelp foo];   // Polymorphism to the rescue
}

@end



If you really want, you can  have 

    
@interface B : NSObject
// This is a helper class for instances of A
{
   id         theInstanceOfAnyClassToHelp;
}

- (void)setInstanceToHelp:(id)anA;
- (void)doSomething;

@end


@implementation B

-(void)dealloc
{
   [self setInstanceToHelp:nil];
   [super dealloc];
}


- (void)setInstanceToHelp:(id)anA
{  // anA can be any kind of A including an instance of some subclass of A
   [anA retain];
   [theInstanceOfAnyClassToHelp release];
   theInstanceOfAnyClassToHelp = anA;
}


- (void)doSomething
{
   if([theInstanceOfAnyClassToHelp respondsToSelector:@selector(foo)])
   {
       [theInstanceOfAnyClassToHelp foo];   // Polymorphism to the rescue
   }
}

@end




If you are looking for something more than your problem statements explicitly states, consider the a Design Pattern: http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaDesignPatterns/chapter_5_section_2.html
http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaDesignPatterns/chapter_5_section_3.html

***Decorator**
Attach additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality. As does subclassing, adaptation of the Decorator pattern allows you to incorporate new behavior without modifying existing code. Decorators wrap an object of the class whose behavior they extend. They implement the same interface as the object they wrap and add their own behavior either before or after delegating a task to the wrapped object. The Decorator pattern expresses the design principle that classes should be open to extension but closed to modification.
*

***Delegation**
Delegation is a mechanism in which a host object embeds a weak reference (an unretained outlet) to another object&#8212;its delegate&#8212;and periodically sends messages to the delegate when it requires its input for a task. The host object is generally an &#8220;off-the-shelf&#8221; framework object (such as a NSWindow or NSXMLParser object) that is seeking to accomplish something, but can only do so in a generic fashion. The delegate, which is almost always an instance of a custom class, acts in coordination with the host object, supplying program-specific behavior at certain points in the task (see Figure 4-3). Thus delegation makes it possible to modify or extend the behavior of another object without the need for subclassing.
*

Cocoa is widely regarded as the best (or at least first recognized) example of several design patterns, and learning how Cocoa works will teach you a lot more than just how Cocoa works.

----

Thanks, I'll look into Decorators and Delegates, this might solve my problem.

-natmartin

----
Also, this is very basic stuff.  You need to consult the available documentation about object oriented programming and Objective-C.  This was not even a Cocoa question.  The answer is valid for almost any framework and could easily have been translated to C++,Java,C# etc.

Start here: http://developer.apple.com/referencelibrary/GettingStarted/GS_Cocoa/index.html
or http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/index.html

Most likely, these documents are already on your hard disk.

----
I realize this wasn't specifically about cocoa, but I was wondering if there were some objective-c specific tricks I might be able to use. I am not new to programming or cocoa/objective-c, but I am entirely self-taught, so there are certainly gaps in my knowledge. I've read through documentation, but I've been unable to find anything similar to what I am trying to do. If there was a more appropriate place for me to ask this question, please let me know.

-natmartin

Look at using Composition, rather than Inheritance. Perhaps a MoveableGameObject could have a Camera, not be a Camera. This works nicely with Delegation.
http://www.c2.com/cgi/wiki?CompositionInsteadOfInheritance


----

Similar to the solution above, I present this solution (in simplified code, just to show which interfaces and protocols are involved):

    
@protocol A
    - (void)importantMethod;
@end

@interface AbstractA <A> { } // has common methods

@interface SubA1 : AbstractA { } // implements importantMethod in one way

@interface SubA2 : AbstractA { } // implements importantMethod in another way

@interface B <A> {
    id <A> helperObject;
}


This is composition, properly implemented. The solution above is perfectly OK, especially in ObjC, but to make it more formal you define a protocol which declares the methods that the classes must have (    importantMethod in this case). Then you have the abstract superclass (    AbstractA) which implements the protocol A and contains the basic method implementations.

The subclasses     SubA1 and     SubA2 overrides     importantMethod with their specific implementations.

Finally the class     B implements the     A protocol and contains an instance of another object that implements     A and delegates all messages to that object.

The difference between this and the solution above is that we have correct types all the way,     B is an     A but the implementation is still in     ASub1 or     ASub2 or any other object that implements     A for that matter.

--TheoHultber/Iconara

