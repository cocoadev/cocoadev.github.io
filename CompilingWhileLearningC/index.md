---
layout: page
title: CompilingWhileLearningC
---

CompilingWhileLearningC .  My last code was written on my little llc, before hard drives  and I am now attempting to learn C, then Objective C with XCode.  Is there a way to  write some simple code in C, compile it, and run it without 1st learning all the XCode targets, instances, etc.?  
I just want to practice some of the simpler stuff, like:

    
#include 
int main()
{
    int a, b, c;
    printf("Enter the first value:");
    scanf("%d", &a);
    printf("Enter the second value:");
    scanf("%d", &b);
    c = a + b;
    printf("%d + %d = %d\n", a, b, c);
    return 0;
}


and compile/run them, before advancing to the more complicated stuff...
Will the terminal do this, or XCode? and if so, how?
Thanks much

----

Make a new "Standard Tool" project in Xcode. Building and running is a simple task, as is editing the main.c file which will be created for you. You can learn Xcode in a gradual fashion from there.

----

You might want to check out the "Learn C on the Macintosh" book by Dave Mark.  It walks you through learning C and using Xcode.

----

I've been getting my feet wet with Cocoa by using Terminal.app and a text file.

It seemed easier for me to get acquainted with obj-c and Cocoa this way.

Take a look at:
http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaObjects/Articles/SimpleCocoaTool.html

And from the terminal compile the program with:

gcc -v -framework Foundation -o simpleCocoa simpleCocoa.m

----
Your simple example doesn't need any included libraries, so it will compile in the terminal easily:
    
make code.c

Run in the terminal: ./code
EnglaBenny

