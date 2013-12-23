---
layout: page
title: FakeNSObject
---



----

Hi all,
I was trying to emulate NSObject's retain count in C++ (off topic? I hope not)... I soon came up with:

    
#define N_ALLOC_INIT(class) (class *) class::alloc ()->init ()

#define N_BASE_CLASS(name) \
 public: \
  static id alloc () { return new name; }

class NObject;
typedef NObject * id;

class NObject
{
public:

 virtual id init ();
 virtual void dealloc ();

 id retain ();
 void release ();

 unsigned int getRetainCount ();

private:

 unsigned int retainCount;
};

static inline void NSafeRelease (id object)
{ if (object != NULL) object->release (); }

......

NObject::init ()
{
 return this;
}

.....
(the obvious)
.........


The problem is (now) about init methods in subclasses:

    
id NASubclass::init ()
{
 objectA = N_ALLOC_INIT (NObjectA);
 objectB = N_ALLOC_INIT (NObjectB);
......
 objectF = N_ALLOC_INIT (NObjectF);

 return NObject::init ();
}

void NASubclass::dealloc ()
{
 ..........
 NSafeRelease (objectB);
 NSafeRelease (objectA);
 NObject::dealloc ();
}


If I wanted the object to automatically release itself inside method init if one of its own objects failed to initialize (being NULL) I would come up with **ugly** code in init methods:

i.e.:
    
id NASubclass::init ()
{
 .....
 objectF = ......

 if (objectA != NULL && objectB != ...................................)
  return NObject::init ();
 else
 {
  release ();
  return NULL;
 }
}


Any elegant solution ? Cocoa ppl should be used to them :)
Anyone? Thx

-- peacha

----

I tried to make a neater solution, but it failed. -- KritTer

----

I was able to see, but not to reply to it in time (my provider SUXXX)
Thanks anyway, even though I still have that init "doubt".....
What about your implementation of delete calling C's stdlib free: would it --always-- work for stack allocated objects (even though I'm not going to support them, just curiosity)?

-- peacha

I was trying to stop stack allocation, leaving only heap alloc, but that's impossible. I also couldn't implement deletion properly because the destructor for the object is called before operator delete, so the object was going squiffy even if it was retained. All in all, I decided to throw in the towel.

-- KritTer

