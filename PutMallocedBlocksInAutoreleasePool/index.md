---
layout: page
title: PutMallocedBlocksInAutoreleasePool
---

In the process of wrapping a Cocoa gui around a C++ library, I've come across a case where it would be
convenient (or so I think) to stash some dynamically allocated memory (from malloc) on to the AutoreleasePool.
I've written the following code which I hoped would make this happen, but it seems to leak anyways. (i.e. the MemoryHolder
is never deallocated, and hence the ptr is never free'd either).

Does anyone know why?  Is there a better way to do what I'm trying to do?

    
@interface MemoryHolder : NSObject
{
    void *ptr;
}

- (id)initWithPointer:(void *)ptr;
@end

@implementation MemoryHolder
- (id)initWithPointer:(void *)_ptr
{
    if (self = [super init]) {
        ptr = _ptr;
    }
    return self;
}

- (void)dealloc
{
    free(ptr);
}
@end


The usage is something like this:

    
char *contrivedExample() {
    char *buf = (char *) malloc(10);
    [[[MemoryHolder alloc] initWithPointer:buf] autorelease];
    return buf;
}



-- WendellHicken

----

try this

    
- (void)dealloc
{
    free(ptr);
    [super dealloc];
}
@end


----

D'oh!  Yep, that fixed it.  Thanks!

----

There's no need to write a custom class for this. NSData can do it, by using     -initWithBytesNoCopy:length:freeWhenDone:.

----

Why would anyone use malloc/free in C++ code? ;)

----
Becuase operator new() and operator delete() use malloc/free.

----
You certainly do not want to do:
    
char* buf = new char[32];
...
free(buf);

This is wrong on so many levels...

