---
layout: page
title: HowToAccessMethodsAndVariablesFromADifferentClass
---

To use functions from one class (myFirstClass) in another class (mySecondClass), create an outlet from mySecondClass to myFirstClass.  Here you use Project Builder and go into the code of mySecondClass.h.  You add the following declaration.
    
	IBOutlet myFirstClass *myFirstClass;


Connect this outlet in Interface Builder
Now you can call class one methods like this
    
	[myFirstClass aMyFirstClassMethod];


You cannot access the variables directly, but you can call accessor methods.  If you are in mySecondClass and you want a variable from myFirstClass called x, then do the following.  In myFirstClass you can create the functions
    
-(void) x;
-(void) setX:newX;


Here the code for x is:
    
	return x;


And the code for setX:newX is
    
if (newX !=x)
{
	[x release];
	x = [newX retain];
}


Now you can use the method x to get x like this
    
	y = [myFirstClass x];


You can also set x to a different value by doing this
    
	[myFirstClass setX:10];




Go back to HowToProgramInOSX

A newbie's annotation: Some information about @property and the dot syntax wouldn't be bad here either. This puzzles my as newbie at the moment..

----
Some of this is fairly misleading, starting with the title. Here's a short list of things to change:

*The title is HowToAccessMethodsAndVariablesFromADifferentClass, but it should really be HowToAccessMethodsAndVariablesFromADifferentObject. Generally speaking, you send messages to objects, not classes. A class is a kind of object and can receive messages, but that's not what's shown in the text.
*You can send a message to any object to which you have a valid reference. Getting that reference via an outlet is one way to do it, but there are lots of other ways too.
*The text indicates that you can't access another object's variables directly, but that's only kinda-sorta true. There should be some reference to the different protections classes can declare for their variables (@public, @protected, @private, @package). The @protected state is perhaps the most confusing for newbies, and it's the default, so it should certainly be explained.

Perhaps the best solution would be to scrap this page and simply add any important material presented here to a page on how to send a message. -CS

