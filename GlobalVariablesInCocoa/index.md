---
layout: page
title: GlobalVariablesInCocoa
---

As a newbie Cocoa programmer, it was difficult to find information about global variables anywhere, without being treated with a condescending 'go learn C' type of response. Everyone tells you that global declaration is such a stupidly simple thing to do, and yet, no one is interested in spending the 10 minutes needed to tell you just HOW to do it...

While globals, in general, should be avoided, the truth is that there *will* be times, in just about *every* program, when global variables (or constants) are needed. For one, if you use a global constant, instead of a simple string literal, the compiler will warn you of any typing mistakes! http://goo.gl/OeSCu

First of all, let's start by saying that a very popular strategy for organising global variables in a program, is to keep them all together, in a separate source file. The nature of globals implies that you are going to be accessing them on several different areas of your program, so rather than placing globals inside this or that specific class source code, put them in a separate file - called 'GlobalValues', or something like that. That way, if you have to make a change to any global, you will easily find it. Adding globals is made easier as well, as any classes that already import the 'GlobalValues' source will automatically have access to any other globals you define there in the future.

**FIRST SOLUTION: THE C WAY** 

The C language has a way for us to define 'macros'. These 'macros' are just bits of text that get replaced - at compile time - by other bits of text. For instance, I could define the term "SALARYRAISE" to be substituted by "0.5". That means that wherever I use the term 'SALARYRAISE' in my code, the compiler will replace it with '0.5' just before it compiles. The advantage of this, is that if I decide to change the salary raise from 0.5 to 0.6, all I have to do is to replace it in the macro definition. If I were using the actual number '0.5', I would have to track it everywhere around my code, and change it manually in all the places I am using it. To define these text macros in C, we use the #define directive, like this:

    
#define SALARYRAISE 0.5


So, if the global variables that you want to access are actually global *constants* - ie, values that you know as you write the code, and that are not going to change at runtime - then you can declare them as C macros:


*1) Add a new file to your project. Let's call it "GlobalValues.h".
*2) In the "GlobalValues.h" file, define your constants
*3) In the beginning of the source files that need to access the constants, import (with #import) your "GlobalValues.h" file


Example:

A "GlobalValues.h" file that defines a couple of string constants:

    
//name of the 'service' the application uses for storing items in the user's keychain:
#define MYKeychainServiceName @"MyApplication Password"

//name of the PasteBoard data type that application uses for document data:
#define MYPboardDataType @"MyApplication DocData"


In the implementation files of the classes that need to use these values, you would put - at the very beginning:

    
#import "GlobalValues.h"


*(Note: changed to use a .h file - #importing a .m file is Evil.)*

**SECOND SOLUTION: THE OBJECTIVE-C WAY** 

Another option, which gives you a little more flexibility than the C's 'define' macros, is to declare the globals as 'external' variables - variables that, in C, are defined with the 'extern' keyword. This is the most-frequently used solution in the Cocoa community. It allows you to separate the variable declaration and the variable definition into separate files, so that someone using your variables does not necessarily have to know what their *actual* original value is. Also, being a *variable*, means that you can actually use these values like any other values throughout your program - changing and manipulating them, if need be.


*1) Add 2 new files to your project: "GlobalValues.h" and "GloblaValues.m".
*2) Open "GlobalValues.h", and declare all your needed variables.


    
//name of the 'service' used for storing items in the user's keychain:
extern NSString *MYKeychainServiceName;

//name of the PasteBoard data type that application uses for document data:
extern NSString *MYPboardDataType;



*3) Open "GlobalValues.m", and start the new file by importing "GlobalValues.h". 
*4) Then, assign values to the variables you declared in the header file:


    
#import "GlobalValues.h"

//name of the 'service' the application uses for storing items in the user's keychain:
NSString *MYKeychainServiceName = @"MyApplication Password";

//name of the PasteBoard data type that application uses for document data:
NSString *MYPboardDataType = @"MyApplication DocData";


In the implementation files of the classes that need to use these variables, you would put - at the very beginning:

    
#import "GlobalValues.h"


I hope this helps!

----

One final approach to supplying global-like objects is with a class method. This is rather over-the-top for a simple global, but is handy when it may not actually **be** a global object, as you neatly encapsulate any nasty implementation details. Apple uses this trick; take, for example,     [NSWorkspace sharedWorkspace], which essentially returns a global variable, and *could* return a global variable (say, a proxy), but which actually doesn't.

Doing this is nice and easy. You'll need a .h file, for the class wrapper, and a .m file, to allocate memory for the globals.

    <GlobalValues.h>
@interface MyGlobals
{ }

//name of the 'service' the application uses for storing items in the user's keychain:
+(NSString *)keychainServiceName;
//name of the PasteBoard data type that application uses for document data:
+(NSString *)pboardDataType;
@end


    <GlobalValues.m>
#import "GlobalValues.h"

NSString *MYKeychainServiceName = @"MyApplication Password";
NSString *MYPboardDataType = @"MyApplication DocData";

@implementation MyGlobals

+(NSString *)keychainServiceName
{ return MYKeychainServiceName; }
+(NSString *)pboardDataType
{ return MYPboardDataType; }

@end


Now, to access a global, we use     [MyGlobals keychainServiceName] or     [MyGlobals pboardDataType].

The advantage of this pattern is encapsulation and flexibility. Suppose we wanted to load the keychain service name from a preferences file when used: we can easily do this with the accessor pattern, but implementing it with a raw global will be harder.

You probably shouldn't use it when a global variable would do, though.

