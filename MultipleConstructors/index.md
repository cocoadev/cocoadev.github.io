---
layout: page
title: MultipleConstructors
---

My class has around twenty instance variables which are initialized through three different constructor methods depending on how the object needs to be built.  In all three cases, though, around eighteen or nineteen of the variables are identical.  

What I would like to do is eliminate having to repeat the code that sets the common valued variables in each constructor (somehow only include the common code once).  The only thing I want the three different constructors to do is set the one or two variables that could have a different value depending on which constructor is called.  Of course, the object then has to be returned.

Can I do this by having each constructor call another method to set the common values (that does NOT end in a "return self;" statement) and then perform the "return self;" in the calling constructor?  Will this work?  Is this the best way to do this?  Is there a more standard way?

Thanks,
KentSignorini

----

Yes this will work. In fact, it's how Cocoa's     initWith... methods are implemented.

----

One of the really nice things about Objective-C is that constructors aren't special at all (we only call them `constructors` so everyone will know what we are talking about).  This means you can do things like:

    

- (id)init
{
self = [super init];
if (self != nil)
{
//initialize some variables
}
return self;
}

- (id)initWithSpecialArgs:(id)someStuff
{
self = [self init];
if (self != nil)
{
//initialize your special stuff
}
return self;
}



I think that it is preferred to make this happen in the other way but I, personally, like this way and it illustrates my point quite well.

The important thing to realize is that     init is just some other method.  The     alloc or     allocWithZone: messages are the ones that actually allocate the memory.  Even there, there isn't anything special about those names.  It is just a convention.

--JeffDisher

----

I'm sorry for a silly question, but don't you think that     
self = [self init];

doesn't make sense? --KonstantinAnoshkin

----

It makes sense in that the value returned by     [self init] may not actually be the original value of self.  By re-assigning the pointer, future calculations relative to it will be correct.  Plus, it is conceptually the same as any other call so it makes logical sense to do it.  This is very important to realize when you consider that you may be instantiating something which wants to change the object as it is instantiated.  Class clusters make heavy use of this, for example.  As a good example of this, consider that your     init method has to do some sort of sanity check before initializing the object's instance variables.  If the sanity check fails, the init method will probably want to deallocate itself.  Thus, it would send     [self release] and then would return nil to its caller.  If that were to happen, the re-assignment of self to nil would mean that the next line which checks its value would fail and the initializer would return nil (as it should).  If you don't re-assign the value and just assume that it is the same, you would try to access instance variables relative to this old self you would be trying to read or write memory at a now deallocated memory location and would cause a seg fault.

Does that make sense?

--JeffDisher

----

Yes, thank you. I was just confused by this example's simplicity, in which it doesn't do anything: since     -init returns     self, it's pointless to do     self = self. In less obvious cases, such as class clusters, it does make sense. --KonstantinAnoshkin

----

It is unsafe to leave out lines like     self = [self init]; because a subclass may override some of the init methods.  You'd have to be able to remember/guarantee that no subclasses would do anything funky.

It would be more normal, in the above example, for the plain     init method to call     - (id)initWithSpecialArgs:(id)someStuff with some default values instead of the other way around.  Then     - (id)initWithSpecialArgs:(id)someStuff  is the designated initializer and it's the only init method that needs to be overridden by subclasses.

    

- (id)init
{
return [self initWithSpecialArgs:defaultValue];
}

- (id)initWithSpecialArgs:(id)someStuff
{
self = [super init];
if (self != nil)
{
//initialize your special stuff
}
return self;
}



But also:  What the original poster was suggesting is also fine.  Say you're inheriting from NSView, which has two designated initializers:

    

- (id)initWithFrame:(NSRect)frameRect
{
  self = [super initWithFrame:frameRect];
  if (self != nil)
  {
    [self setUpMyView];
  }
  
  return self;
}

- (id)initWithCoder:(NSCoder *)decoder
{
  self = [super initWithCoder: decoder];
  if (self != nil)
  {
    [self setUpMyView];
  }
  
  return self;
}

- (void)setUpMyView
{
//do your initialization
}


 


-KenFerry

