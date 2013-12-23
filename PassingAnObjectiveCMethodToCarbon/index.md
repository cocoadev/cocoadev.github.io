---
layout: page
title: PassingAnObjectiveCMethodToCarbon
---

How do I pass a pointer to an Objective-C method to a Carbon function?

The Carbon function normally would take a pointer to a function like "&myFunctionName" to use it as a callback.

But when (within one of my methods) I call the Carbon function and try to pass it "&myMethodName" for instance it says "&myMethodName" is undeclared.

I've been trying to read about selectors & such but I still haven't found anything that actually works.

----

First, you need to get the IMP-- implementation-- of the method via NSObject's messages to do this. Second, it probably won't work as a callback since all ObjC methods take an id and a SEL as their first two arguments. Perhaps better to just write a function, use that as your callback, and have it call your message.

----

In ObjC, you need an object and a selector. Generally, your object can change and your selector stays constant. Carbon lets you pass in a void * to use for sending context info to yourself, which you can use to pass self. Put it all together, and you get something like this:

    void MyCarbonCallback(..., void *userData, ...)
{
   id object = (id)userData;
   [object carbonCallbackWithThing:...];
}


Then you can pass MyCarbonCallback to your carbon functions.

----

You will almost certainly also need to wrap an autorelease pool round this.

----

I use this technique with Carbon global hotkeys and didn't need an autorelease pool. Most likely the Cocoa event loop is still handling the event and setting up the pool before Carbon gets ahold of it, or something like that. This may not hold for all events, of course, so if you start getting lots of "no autorelease pool in place" messages, then set one up.

----

Good point, I hadn't thought about that because my experience of mixing cocoa and carbon has mainly been Cocoa in a Carbon app.

----

one other thing: you don't need & to take a function pointer. you simply use the name of the function.

**LANGUAGE LAWYER MOMENT**

the function call operator is a pair of parentheses containing zero or more expressions separated by ',', following a function pointer. so you can do any of these:

    main(argc, argv); //pointer to main is taken, then called
int (*mainptr)(int, const char **) = main; //store a pointer to a
//  function that takes an int and a const char **(like main) as
//  arguments and returns int
mainptr(argc, argv); //call the stored pointer to main
(&main)(argc, argv); //WRONG! this calls a pointer to a pointer to main!


**END OF LANGUAGE LAWYER MOMENT**

worth noting: when I compile test code of an '&foo' construction in gcc, I get link errors. the code:

    #include <stdio.h>

void foo(void);

int main(int argc, const char **argv) {
        fputs("calling foo\n", stdout);
        foo();
        fputs("calling &foo\n", stdout);
        (&foo)();

        return 0;
}

void foo(void) {
        fputs("foo called\n", stdout);
}


and the errors:

    gcc fptr.c -std=c99 -Wall -pedantic fptr.c -o fptr        %/Volumes/RAM Disk(1)
ld: multiple definitions of symbol _foo
/var/tmp//ccKSoC9E.o definition of _foo in section (__TEXT,__text)
/var/tmp//ccgAf3Y8.o definition of _foo in section (__TEXT,__text)
ld: multiple definitions of symbol _main
/var/tmp//ccKSoC9E.o definition of _main in section (__TEXT,__text)
/var/tmp//ccgAf3Y8.o definition of _main in section (__TEXT,__text)


*--boredzo*

----
You're getting errors because you included     fptr.c twice in your command line.

Also,     function and     &function return identical results in C. They have the same type and the same value.

----
Hmm.  I didn't have any problem with your code using cc (gcc) without options (see below).  I did get
the error with the     pedantic option.  Why are you using it?  Also, why not use     printf( "Message" );
instead of     fputs()

    
b$ cc testPtrFunc.c
b$ ./a.out
calling foo
foo called
calling &foo
foo called
b$ cc -v
Reading specs from /usr/libexec/gcc/darwin/ppc/3.3/specs
Thread model: posix
gcc version 3.3 20030304 (Apple Computer, Inc. build 1666)

