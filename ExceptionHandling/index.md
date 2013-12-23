---
layout: page
title: ExceptionHandling
---



[Topic]

----

MacDevCenter has recently posted an article on this topic:  
Understanding Exceptions and Handlers in Cocoa [http://www.macdevcenter.com/pub/a/mac/2007/07/31/understanding-exceptions-and-handlers-in-cocoa.html]

----

This whole section needs to be rewritten to address @try/@catch/@finally.

----

If something goes wrong in Cocoa, it may choose to "raise an exception".

When an exception is raised, the flow of execution of the current function is interrupted.  Control immediately leaves the function, and resumes at the nearest exception handler.

Exception handlers look like this:

    
NS_DURING
    doThingsThatMayRaiseAnException();
NS_HANDLER
    // deal with any exception
NS_ENDHANDLER


This is somewhat analogous to a C++ try/catch block, but ultimately resembles more closely a set of pre-processor macros built atop C's setjmp/longjmp. In C++, one "throws" an exception and objects on the stack are destroyed on the way to the matching 'catch'; this does not happen in Objective C; the closest you can get to C++ exception handling in Objective C is judicious use of autorelease. Garbage collection makes this model virtually impossible as finalizer functions are expected to do almost nothing.

If no exceptions are raised, the code between NS_DURING and NS_HANDLER will execute all the way through, and none of the code in the handler will be executed.

However if an exception occurs, control will leave the NS_DURING block and resume at the first line of the NS_HANDLER block.

Although you can't see it, an object named "localException" of type NSException is available within the scope of the handler.  This object encapsulates information about where and why the exception occurred.  Refer to NSException's documentation for more details.

You can also choose to raise your own exceptions within your own code, by sending the "raise" message to an NSException.

Note that you can choose to re-raise localException from your handler, if you want to pass the exception up to the next higher handler.

If an exception occurs outside the scope of any handler, your application will terminate. Since NSApplication supplies its own handler during event handling, most of the time an otherwise uncaught exception causes the current event-handler to immediately halt, and a message is printed to the console. Though the application does not terminate it may leave your application in an "unpredictable" state, depending on what your app was doing at the time the exception raised.

It is possible to cause a debugger break whenever an exception is raised.  See DebuggingTechniques to learn how this is done.

Also, we have a trick that enables you to get StackTraces in Obj-C similar to what you might be used to from Java.

----

**Discussion and questions**

One thing to keep in mind is that the Cocoa exception handling scheme isn't as flexible as C++ or Java.  The latter two can throw objects, and you can do nice things like in one exception block catch file-related errors with some code, and catch SQL-related errors in the other.   In Cocoa all the 'built-in' exceptions are just NSStrings.  You can probably achieve the latter with a bit of subclassing, but it runs counter to what Apple does.

With that said, the exceptions I've noticed being thrown from the toolkit are of the "bad argument" and  "something really bad happened that I can't correct on my own" variety.

----

"Bad argument" exceptions annoy me no end; assertions would seem to fit better. -- RobRix

I like to use assertions inside of my code to sanity check and catch my problems, and use bad argument exceptions when someone using my toolkit gives me something bad.  If something gets tossed at runtime, it makes it easier to point the finger of blame :-)  ++MarkDalrymple

----

What about finally-blocks? I can see that there is no room for them in the current syntax, but they sure are nice to have. The ObjC DURING/HANDLER syntax sucks, try/catch/finally is much more integrated into C-style syntaxes. --TheoHultberg/Iconara

----

The new way of handling exceptions (in XCode/GCC3.3 presumably) looks like this:

    
@try {
     codeThatMightThrowAnException;
}
@catch ( NSException *e ) {
     dealWithTheException;
}
@finally {
     cleanUp;
}


*

"Using [this feature] in a program, renders the application runnable only in Mac OS X v10.3 and later because runtime support for exception handling and synchronization is not present in earlier versions of the software."

http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/LanguageOverview/chapter_3_section_9.html
*

GCC documentation:

xCode <= 1.2 file:///Developer/Documentation/DeveloperTools/gcc-3.3/gcc/Objective-C-Dialect-Options.html#Objective-C%20Dialect%20Options

xCode >= 1.5 file:///Developer/ADC%20Reference%20Library/documentation/DeveloperTools/gcc-3.3/gcc/Objective-C-Dialect-Options.html

Although currently designed to be binary compatible with NS_HANDLER-style idioms provided by the NSException class, the new exceptions can only be used on Mac OS X 10.3 (Panther) and later systems, due to additional functionality needed in the (NeXT) Objective-C runtime.

In this case, "binary compatible" means that you can @catch exceptions that were thrown using -[NSException raise], and you can @throw exceptions that will be caught by an NS_HANDLER, without having to recompile your old-style macro-using code on the new system. This means any libraries you have that throw or catch exceptions can be used with the new @try syntax without recompiling.

----

How can I break when an exception is raised (i.e., out of bounds, or one of my own) or, conversely, is there any way I can get a stack trace of where the exception was thrown?

----
Breaking on exceptions in XCode 4:
* Show the Breakpoint Navigator (left-size panel)
* Click "+" popup menu, choose Add Exception Breakpoint...
* Choose Objective C, C++ or both types of exceptions
In XCode 3, 
* Click the Breakpoints toolbar button (or select Breakpoints from the Debug menu. For Project Builder,  click on the "Breakpoints" tab in your project window.
* Click the "New" button
* Enter the following text "-[NSException raise]" (without the quotes)
 

----
This will cause the debugger to break every time it encounters an exception, which will allow you to examine the call stack and determine exactly where the problem occurred.

Also see DebuggingTechniques for a possible solution.  It mentions the commands to do a back trace in gdb.

----
In OS X 10.5, -[NSException raise] is no longer called. Set a breakpoint on objc_exception_throw instead.

----

I'm trying to raise an exception that actually halts the program.  Often I want to catch exceptions and handle them gracefully, and I've been using NS_DURING, NS_HANDLER, and NS_ENDHANDLER for that.  But, sometimes, if for example I know some int x has to have a value at least 3 at this time and if it doesn't I've programmed the thing wrong, then I want to throw a fatal (uncaught) exception.

Apple says, "The default uncaught exception handler logs a message in the console and exits the program."  But when I raise an exception, all I get is a log message and the program keeps going.

----

Exceptions on the main thread are caught by NSApplication. I'm not sure how to override this, the docs aren't clear, but I think you use the NSExceptionHandling (or NSExceptionHandlers) framework, which is undocumented except for file:///Developer/Documentation/Cocoa/Conceptual/Exceptions/Tasks/ControllingAppResponse.html. �DustinVoss

----

http://developer.apple.com/documentation/Cocoa/Conceptual/Exceptions/index.html

Take a look at NSSetUncaughtExceptionHandler; I suspect there is nothing from stopping you from overriding this behavior and bringing the program down.

----

I've heard from someone who tried this. It won't affect exceptions thrown on the main thread, because those exceptions *are* caught�by NSApplication. �DustinVoss

----

You can use an assert() for this instead. 

#include <assert.h> and place assert(x>=3) where needed. Program will abort() if assertition is false with file name and line number on stderr. Do a man assert for more information.

----
Hi, last time I checked (OS X 10.2), Obj-C exceptions used setjmp/longjmp (why couldn't they fit the 'u' in the name?), which is incompatible with C++ stack unwinding (right...?). This is unacceptable for me, since I write a lot of Objective-C++, but it is also inconvenient for my libraries to be exception illiterate. I really don't know if I will use a given library with C++, Objective-C++, or just plain Objective-C. I solved this problem the way I solve most of my problems... by ignoring it(*wink*). I just did not use exceptions in Cocoa. 

Now one of my older libraries is on the review sheet, and I really like it, but it sorely needs exceptions. The problem is that the library is written in Obj-C++, and is used in both vanilla Cocoa, and Obj-C++ code. I would like it if I can handle exceptions reasonably in both environments. My current solution (currently unimplemented, it just lives in my head) calls for a custom     void onException (...) type callback which can be installed into the library by clients (the argument list will not be variadic, I just have not decided what good error args are yet). This function can then throw a Cocoa or C++ exception, or log the error in some thread specific variable like     errno.

I have descibed my predicament in the hope that someone has already solved something like this before, I am well aware that there are several problems with my proposed 'solution'. So my post boils down to a couple of questions: What is the current state of Obj-C and C++ exception interaction? If my info is not obsolete and Obj-C exceptions don't support C++ stack unwinding, then what options do I have? Currently I am considering the solution I have proposed above, as well as MACRO's (*yuck*), and just using plain C++ exceptions and saying *fudge off* to pure Cocoa developers. The last option is the easiest technically, and worst philosophically, so I am hoping someone can help my avoid it.

--JeremyJurksztowicz

----

Hi, Jeremy -- I don't have the most infinitesimal clue about the current state of exception interaction between ObjC and C++, but I will offer you this quote from the venerable Apple TN2034, though it may not be relevant here, and very little of which should be news to you:

*The statically-bound nature of C++ class libraries is generally incompatible with the dynamic code loading and software update features of Mac OS X. If there is any chance that your favorite collection of useful functions will ever need to be  packaged into a shared library, do not use C++. Good old 'C' is the preferred language for performance-critical shared  libraries. Where it's necessary to export object-oriented interfaces, both Java and Objective-C provide dynamic class loading mechanisms that work well with Mac OS X.*

    voidOnException is a delightful contribution to the namespace. It brings visions of an app peeing in its pants.  :-) I'm sympathetic to the devil's bargain (or as you term it, "predicament") that you have entered into with Bjarne's invention, and hope that you will implement this, rather than the terser "fudge off" message. If you choose the latter, don't hold back, censoring your language like that.

Respectfully yours, BoozeDog

----

I guess I'll take this technote as a sign that exception interoperability is not a priority for Apple. Argh, I am forced to use C++ (not that I'm complaining) because I am using a portable wrapper around the CoreAudio utility libraries released so graciously by Apple, which are all in straight C++. 

Ok, one more idea to throw by y'all, I think this one is the closest to acceptable that I have gotten. Since the objects which will be throwing exceptions are created by an Obj-C factory object, I was thinking that I could have a selector to install a error routine (wether this is a function or Obj-C selector, I have yet to decide), for all newly created objects. Thus, C++ modules could install a C++ exception thrower into the factory, and then create the needed objects, which now throw C++ exceptions on errors. Similarly the Cocoa modules can use Obj-C exceptions, while the straight C modules could install a routine to fiddle with errno. The last hurdle is ensuring that a given module is aware of the kind of error reporting a given object instance is using. While this caveat sounds major, In the average Cocoa app, this would not be an issue. It would only be a problem for monkeys like me who write programs with C, Obj-C, C++, Java, Python and AppleScript modules. Of course I am surely missing something, so comments are appreciated. 

-- My name is JeremyJurksztowicz and I am a language-o-holic

----
I'm not sure if it will be a problem, but to implement this idea you will have to deal with the fact that the non-interoperability goes deeper than just not catching the other languages exceptions. In particular, you can't:


*Throw ObjC exceptions through C++ code which has stack-allocated objects with destructors. The destructors will not be invoked.
*Throw C++ exceptions through ObjC code which contains an ObjC exception handler or the     @synchronized keyword. The appropriate cleanup code for each will not be invoked.
*Throw C++ exceptions through ObjC code that you don't own. This means things like the runloop, notification sending, delayed performs, distributed objects invocations, NSInvocation invocations, and anything else where there's code you didn't write yourself on the stack between the exception being thrown and the catch statement. This is because any of this code could be doing the stuff above, or it could simply have its own cleanup code which it expects to be called and which now won't. Note that     objc_msgSend and friends do not count for this because they are implemented as trampolines and are off the stack by the time your code runs.
*The safety of throwing ObjC exceptions through a C++ try/catch block will be compiler-dependent. I believe that gcc does not need any cleanup code to run in this case so it should be safe, but it is something to keep in mind.


----

I have about zero experience with C++, but wouldn't it be safest just to wrap any Objective-C calls found in C++ code in a @try/@catch block, and to wrap any C++ calls found in Objective-C code in a try/catch block?

----

Just saying, this argument is now _well_ out of date. The ObjC 2.0 runtime has C++ compatible exceptions. So things like "@throw through a try/catch handler" are meaningless now.

