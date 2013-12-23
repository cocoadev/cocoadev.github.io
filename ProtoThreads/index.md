---
layout: page
title: ProtoThreads
---

ProtoThreads is a lightweight cooperative threading model to simplify programming against asynchronous APIs.

An Objective-C version of this library (http://www.frameworklabs.de/protothreads.html ) is available to evaluate the usefulness of this concept on OS X. Material hosted at (http://www.sics.se/~adam/pt/ ) might also be useful.


----

It looks interesting, thanks!

To make it really easy to use, it would be nice to not have to declare the callback method and to not have to deconstruct the NSInvocation, but have some magic macros do it for the programmer. Is that completely unfeasible? Ideally, the code would look like this:

    

- (void)run 
{ 

PTThreadBegin; 

...
ofts = ...; // assign instance variable 
[ofts setDelegate:self]; 
[otfs connectToObjectPushService]; 

// wait for the event-handler to signal
OBEXFileTransferServices *inServices; 
OBEXError inError; 
PTThreadWaitExecution(
	@selector(fileTransferServicesConnectionComplete:error:),
	inServices,
	inError ); 

//continue execution
//...handle error...
[ofts sendFile:file]; 
... 

PTThreadEnd; 

} 

//and, here, no method 'fileTransferServicesConnectionComplete:error:' declared
//this is taken care of by PTThreadWaitExecution



This is giving up some of the flexibility of the macro ' PTThreadWaitUntilKeepingInvocation', and does not have the [self signal:] mechanism, but the above syntax makes it very clean and simple for many occasions where you just want to wait for just one method to be called and get that info right where you are in the code. I know very little about macros, so I don't know if the above is even feasible --CharlesParnot

----

Hi Charles,

I like your idea very much. Indeed, many times you just want to wait for one specific callback to occur.

Creating a macro PTThreadWaitExecution which extracts the arguments automatically should be easy by using variadic macros (i.e. macros which can take a variable number of arguments). Unfortunately I don't see a way how the callback method can be implicitly declared by the same macro as the declaration has to be done outside of a method and the macro-call is inside a method.

Maybe someone has a good idea?

Cheers,
marc

----
Maybe you could write a general-purpose trampoline object, and have it receive the callback, then forward things on from there. Override     forwardInvocation: to trap the callback and do whatever work needs to be done. I haven't looked too hard at this thing so I don't know if it would really work, but that's my idea.

----
Another possibility is then to use the ObjC runtime to add the method on the fly, and use the same IMP for all. One problem will be the same as the problem faced by AspectCocoa in that you can't have a single IMP to handle all kinds of methods, mostly because of non-id arguments. The ForwardInvocation is actually what AspectCocoa uses in extreme cases of unsually typed methods, instead of one of a bunch of prewritten IMP. Anyway, in the case of ProtoThreads, the use of either technique probably makes it more complicated that you would want.

Maybe an alternative is to replace the method declaration by yet another macro, put oustide of the other method, that takes as argument the selector and the types of the different arguments, e.g.:

    
PTThreadHandleExecution(
	@selector(fileTransferServicesConnectionComplete:error:),
	OBEXFileTransferServices,
	OBEXError );


Sorry, this is all I could come up with... and we might as well drop the @selector(..) keyword above. --CharlesParnot

----
Thanks for your inspirating ideas.

I have created a new version of the library which uses an approach as follows:

There is a new subclass of PTProtoThread called PTImplicitProtoThread which overwrites respondsToSelector: and forwardInvocation: so that an explicit definition of the delegate is not needed anymore.

What is needed though (for the sake of NSMethodSignature) is the specification of a @protocol though.

So, what you have to do now in the running example is this:

    

@interface MyThread : PTImplicitProtoThread
@end

@protocol MyThreadProtocol
- (void)fileTransferServicesConnectionComplete:(OBEXFileTransferServices *)fts error:(OBEXError *)err;
@end

@implementation MyThread
- (id)init
{
  if (self = [super initWithProtocol:@protocol(MyThreadProtocol)]) {
    ...
  } 
  return self;
}
- (void)run
{
  PTThreadBegin; 
  ...
  ofts = ...; // assign instance variable 
  [ofts setDelegate:self]; 
  [otfs connectToObjectPushService]; 

  // wait for the event-handler to signal
  OBEXFileTransferServices *inServices;  
  OBEXError inError; 
  PTThreadWaitForInvocation(
	@selector(fileTransferServicesConnectionComplete:error:),
	&inServices, &inError ); 

  //...handle error...
  [ofts sendFile:file]; 
  ... 
  PTThreadEnd;    
}
@end


Cheers
marc

