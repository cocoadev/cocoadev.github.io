---
layout: page
title: HowToUsePointers
---



*Bob: How do you use pointers?

Connie: As little as possible.*

----

Use of the "*" **pointer** operator.

In declaration code like this:

NSColor *color;

the * operator is indicates that the variable "color" is a pointer to an NSColor object. See also HowToDeclareAVariable

The pointer operator is also used to specify arguments that are pointers, e.g., in code like this:

-(void) mouseUP:(NSEvent *)event;

----

    

Class *  // or Class* or Class   * - spaces are irrelevant.



Which should be read as "Class pointer" or "a pointer to Class".

The brackets are only added for argument and return types, like so:

    

(Class *)myObject:(Class *)myArgument withObject:(id)myArgument2....



In C, we use the "*" character to define a pointer-to-variable type. The following code snippet defines a pointer to an integer:

        int *pointer;

In a list of several variables, each pointer variable needs its own pointer operator; if you do this:
    NSObject* myObject1,myObject2:// one's a pointer, one's not instead of     NSObject *myObject1,*myObject2;// probably what you wanted

When you see something like this: (void **)....

    void ** is basically a pointer to a pointer to anything.All pointers have the same size, a pointer to an unknown is defined (by C) as "    void *". The second star makes it a pointer to a pointer.

*More commonly known as a handle.*

----

All of this C stuff is covered in "The C Programming Language" by Kernighan & Ritchie (called the "K and R" or "K&R"). This is one of the best books I have ever purchased and I recommend every man, woman, and child in the greater solar neighborhood should own a copy. ISBN 0-13-110362-8 ... ask for it by name.

Anyway, K&R defines a pointer thusly: "A pointer is a variable that contains the address of a variable". "But what is an address?" you say. Every unit of random access memory in your computer has an "address". The address is a number that identifies a unique piece of memory; kind of like a telephone number or street address. When running a program the computer is processing instructions like "grab the number at address 0x00001a2c and load it into the accumulator, add 1 to the number in the accumulator, file the value in the accumulator in address 0x00001a2c again...". Of course, all you did was write something like this:

        i += 1; 
 
Or, if you're really lazy....

        i++;

The C compiler took care of all of that busy work for you. Hooray for the compiler!

**Assignment and dereferencing of pointers**

We can do two things with pointers: assign them, and dereference them. We can assign pointers arbitrary values, such as NULL:

        int *pointer = NULL;

Since pointers describe addresses in memory, and you can't accurately predict at compile time where something will reside in memory when the program executes, NULL is basically the only value to which you can safely pre-assign pointers. More commonly we use the "&" character to assign pointers to specific addresses:

        int foo = 7;
    int *pointer = &foo;


Here we create an int variable "foo" and initialize it to 7. We also create a pointer to int variable "pointer" and assign it the address of "foo". The "&" operator means "the address of the following variable". We can say the "pointer" variable refers to, or "points to", the contents of the foo variable. We'll find out what that means when we talk about dereferencing pointers.

In C, we also use the "*" character to dereference pointers. Dereferencing means, in the language used earlier, "grab the value at this address". If we were to look at the contents of "pointer" in the sample above we might see a 32-bit number like 0x00001a2c, not the number 7. But if we were to look at the computer's random access memory (RAM) at the address 0x00001a2c, we would see the number 7. More specifically, we would see the contents of "foo", which currently happen to be 7. How do we do that? Like this:

        int foo = 7;
    int *pointer = &foo;
    int bar = *pointer;


At the end of this sample code, foo == 7, pointer is some unpredictable number, and bar is 7. Here's a more interesting example:

        int foo = 7;
    int *pointer = &foo;
    
    printf("pointer points to the number %d\n", *pointer);
    foo = 11;
    printf("pointer points to the number %d\n", *pointer);


We create an integer foo and an integer pointer bar like before. When we reach the first printf, the computer spits out:

        pointer points to the number 7

We are **dereferencing** the pointer, asking it to tell us what it points to. Since pointer points at "foo", "*pointer" returns the contents of foo: 7. The second printf prints:

        pointer points to the number 11

because the contents of foo have changed between calls to printf. How many of you thought the second printf would be the same as the first? The dereference operator is basically a command: "dereference the address and tell me what's there -- RIGHT NOW!". That means every time you dereference a pointer, you might get a different result; that can either be a very good thing, or a very bad thing, depending on if you made a mistake or not. This time, the compiler will not help us.

We can actually do a third thing with pointers: pointer arithmetic. For the love of Pete, just go buy the K&R! I'm not gonna teach you that!

So now you basically understand what pointer variables are and how they work. If not, go back to the top and try reading again. It may take a few tries to sink in. What do you use pointers for? All kinds of things.

The most basic way pointers are used in C is to pass values into functions "by reference". C functions *always* pass variables around "by value". That means when you pass a variable into a function, that function can do whatever it likes with that variable and the client's copy of the variable will not be changed. For example:

    int AddTwoNumbers(int a, int b) 
{
    a = a + b;
    return a;
}

int main (void)
{
    int a = 1, b = 2, c = 0;
    
    c = AddTwoNumbers(a, b);
    
    printf("a=%d\tb=%d\tc=%d\n", a, b, c);

    return 0;
}


The previous code segment returns:

    a=1  b=2  c=3

Since all C parameters are passed "by value", the function AddTwoNumbers has its own copies of the variables a and b. It can change its own copy of a without changing the copy of a defined in main().

We can take advantage of pointers to get around C's "pass by value" behavior for function arguments and effectively pass a value "by reference". When we pass a variable by reference it means "refer to the original variable, don't make your own copy." In a sense pass-by-value continues, it's just the value of the pointer (but then you need to dereference it when appropriate)

See also WhatToKnowAboutCFunctions, and maybe PassAnArrayFromAFunction

*usually you don't use pointers to ints or anything that fits in 4 bytes, except in pass by reference.
*Sometimes you pass by reference, where you tell a function or method where to put the result, using one of the arguments.
You do this to get several results back instead of the usual one. For example:

    void addTwoNumbers(int a, int b, int *result) 
{
    *result = a + b;
}

int main (void)
{
    int a = 1, b = 2, c = 0;
    
    addTwoNumbers(a, b, &c);
    
    printf("a=%d\tb=%d\tc=%d\n", a, b, c);

    return 0;
}


This example displays the same results as the last example, but the mechanism is different. Rather than returning our sum in the return value of addTwoNumbers, we actually modify main's copy of the "c" variable from within addTwoNumbers. The third parameter to addTwoNumbers is a pointer to an integer variable. We can dereference the variable on the left side of our assignment expression to actually change the contents of the address pointed to by the pointer: "add a and b and store the resulting value in the variable pointed to by 'result'". Since we pass the address of main's "c" variable into AddTwoNumbers, the "result" variable points to main's "c" variable.

ObjC programmers use this mechanism all the time when passing objects to other objects. NSMutableDictionary's "setObject:forKey:" method is defined as:

    - (void)setObject:(NSObject*)object forKey:(NSString*)key;

This allows the NSMutableDictionary instance to access the "object" NSObject and "value" NSString instances even though they were created somewhere else, in some other function or method.

The way *I* always think of the parentheses is as a cast. When you are using     (NSEvent *)event, you are in effect telling the compiler to cast the event argument to an instance of NSEvent (in this case also a pointer-to-Cocoa-object), same as     eventVar = (NSEvent *)argument;. Note that object casts do not really exist in the weakly-typed, dynamic language of ObjC; at runtime, all objects are treated as id's. --JediKnil

Pointers are also used to pass C arrays and C strings into functions. That's described elsewhere:

C programmers also use pointers to create AbstractDataStructures like LinkedList, BinaryTree, and HashTable. Objective C objects are fundamentally AbstractDataStructures, for that matter. But this falls outside of the scope of this tutorial. 

**Dynamic memory managment in C: malloc() and free()**

C allows you to use functions like malloc() and free() to create and dispose of memory on the fly. For example:

        int bar;
    int *pointer = malloc(sizeof(int));
    
    *pointer = 7;
    bar = *pointer;
    free(pointer);


Here we reserve enough memory to hold an integer variable and assign the address of that memory to "pointer", initialize this memory to "7", dereference "pointer" and use the resulting value (still "7") to initialize bar, and we destroy the memory being pointed to by "pointer". At the end of the example, bar is "7", and pointer is some unpredictable number. If we move the free command up a line:

        int bar;
    int *pointer = malloc(sizeof(int));
    
    *pointer = 7;
    free(pointer);
    bar = *pointer;


something bad might happen to our program. You see, we are trying to read the contents of memory that has already been destroyed. We don't know what that value might be. Maybe it still points at our old value. Maybe it doesn't. This is where pointers start to get really dangerous.

Anyway, why would you want to create memory? Fundamentally, this is the basis for how all Objective C objects are created: +[NSObject alloc] basically creates memory on the fly to contain your object. This is also the basis for the aforementioned AbstractDataStructures. But while interesting, I'll leave a discussion on malloc() and free() to other books and websites since Objective C has other mechanisms for doing this, so see also:

C allows you to get the address of a function and assign it to a variable. This allows you to have one piece of code call another piece of code on the fly. These are often called "callbacks", since they allow functions to "call back" to the calling code. CF uses C callbacks in a variety of places, such as for managing object creation (CFAllocator). Deep down, function pointers are at the heart of Objective C's dynamic method dispatching: "Find the C function with the name "setObject:forKey:" and jump into it." C callbacks are pretty cool, but I won't go into them here, since Objective C has other mechanisms for doing this.

You'll find a bunch of "Introduction to Computer Science" classes on the web that have interactive labs for teaching pointers. Go read them. -- MikeTrent

----

*99% of the time use this to define an object
    NSObject *myObject (= ...);

and this to use it:
    myObject

prototype a pass by reference function or method like this:

    myMethod:(type **)myObjectRef;// one extra * 

or 

    myFunction(type** myRef);

return the value like this:

    *myObjectRef = valueToReturn;

call the pass by reference like this:
    myMethod:&myObject or     myFunction(&myArgumentToUse)

