---
layout: page
title: HowDoICallaClassMethod
---

I am sure this has been answered but I can't seem to ask this question quite right.

I have a class that has a method to write a file.

-(void)writeafile;

how can I call this method from another class.
do I need to change it to an instance method? When I have the compiler barfs out a multitude of warnings.\

can I not just call this method 

[ClassthatOwnsMethod  writeafile]:

----

class methods need to start with a + not a -... To do what I think you're thinking about doing change the function signature to

+(void)writeFile

and then call it as

[ClassThatOwnsMethod writeFile];

----

So, assuming the other class has a pointer to an instance of your classthatownsmethod, you'd write:

     [instanceOfClassThatOwnsMethod method]; 

May I recommend some light reading?

http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/ObjC.pdf

---- 

I think one of the earlier responders misunderstood your intent because your terminology is off.

*I have a class that has a method to write a file.*

    -(void)writeafile;

*how can I call this method from another class.*

That is not a class method, that is an instance method.  That's the terminology you need to use.  You call it like this:

    [myObject writeAFile:aFile]

