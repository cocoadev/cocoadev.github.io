---
layout: page
title: DevelopmentvsDeployment
---



Development version of a program runs fine but the Deployment Version Fails To Call awakeFromNib.

This problem seems to be very difficult to track down. The Development version with debugging turned runs as expected,
but when I build the deployment version, the Nib loads but awakeFromNib doesn't seem to be called. 
Is there some way to determine why? 

I have put the Xcode project up at:

http://mrmac.mr.aps.anl.gov/~jterry/images/GRAS_Desktop.zip 

I am stumped by this behavior. 

----

On PowerPC, the awakeFromNib method is not called in either case.

The signature of your -init method is wrong.  You have it returning void, but it must return the initialized object.  On intel with a release build, you're probably getting 'lucky' in that the pointer you ought to have returned happened to be in the return register anyway.

(Your build configs are named Debug and Release, by the way.  In older versions of Xcode the default configs were called Development and Deployment.)

----

Actually, your entire -init method is wrong.  Here's what it should look like:

In GRAS.h:
    -(id)init;

In GRAS.m:
    
-(id)init
{
  self = [super init];  // forget this, and you can kiss your app goodbye
  if(self)
  {
    if(!dayNight) dayNight...
    .
    .
    .
    if(!areoEight) ...
  }

  return self;
}


You MUST (well, you'd have to search for a reason not to) call [super init] at the beginning of your -init method, and the way that Objective-C stores the self reference (a hidden parameter to the method) makes the format above the right way to write an initializer.

Just to clarify: the return value on an Intel processor is put into register %eax.  If the last function call you make (which includes Objective-C method calls) puts self in %eax, but you forget to return from -init, then your house doesn't come crashing down.

This is what upsets me about the gcc defaults: they simply warn you if you fail to return from a non-void function -- which for some stupid reason is *still* valid C, just like returning a value in a void function.  And it's also easy to forget that -init should return an id, not void.

----

Thanks all, I now have the program working, but this remains one of the confusing things to me. Who is self being returned to for instance, what type is self? Why doesn't this give an error during compilation? Arggh. 

Anyway, I solved it by:

    
-(id)init
{
     if(!dayNight) dayNight...
    .
    .
    .
    if(!areoEight) ...
 

  return ([super init]);
}


Which does appear to work as well. It is an amazing feature of Cocoa and Mac OS X that I can write what are too me amazing programs without totally understanding what I am doing. 

The finished program appears here, if anyone is interested: http://mrmac.mr.aps.anl.gov/~jterry/images/GRASDesktop.dmg

Appreciate the help. 

----

You should still call [super init] first since otherwise the properties required by the superclass won't be initialized and there will be much more strict requirements on what you can do in the init method (ie:  don't call any methods in the super implementation).  Plus, if the super init implementation fails, it will return nil so you would have been initializing a now-dead object (not wrong, just a waste of time).

You are probably getting away with it since the current implementation of your superclass isn't doing anything interesting.  Still, it is definitely good practice to call it first since it _can_ matter and calling init before actually initializing the fields in your object is never wrong.

