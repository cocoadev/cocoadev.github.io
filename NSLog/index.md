---
layout: page
title: NSLog
---

NSLog is a FoundationKit function for printing debug statements to the console. It is defined in NSObjCRuntime.h:

    
 void NSLog(NSString *format, ...);


NSLog works basically like:

    
 fprintf(stderr, format_string, args ...);


But its format string is a NSString object rather than simple char *, and a timestamp is prepended to the output. You can use it in the predictable ways:

    
 NSLog(@"ALL YOUR BASE ARE BELONG TO US");
 NSLog(@"x = %d, y = %d", x, y);
 NSLog(@"%s\t%s\t0x%08x", "hex", "value", 0x1243);


NSLog will also display meaningful values for well known objects as well:

    
 NSLog(@"my ns string = %@", myNSString);
 NSLog(@"my dictionary = %@", myDict);
 NSLog(@"my image = %@", myNSImage);


NSLog works in this case by asking each object for a string that describes itself, sending the -description method to the objects. (**Note:** if an object doesn't override the description method, you will get the -description implementation inherited from NSObject, which tends to be like <classname : address>. See UsingTheDescriptionMethod

----
Just to clarify, NSLog understands all the conversion code specifiers that printf and company understand with the addition of '@' which is the conversion code for objects. And remember NSString is an object so don't try to convert one in NSLog via 's' which is the C string specifier.

----
The single exception to "all the...specifiers" above seems to be the %.*s specifier many people use to print variable length strings.

----

NSLog is very nice, but be careful which specifier you use.  Consider the following:

    
 int i = 12345;
 NSLog( @"%@", i );   //  Signal 10 SIGBUS error
 
 NSLog( @"%d", i );


i (an int) is not an object! So you can't send     description to it (see above)

**
True, but you could make it one with NSNumber, if you wanted to, like so.
**
    
 NSLog(@"%@", [NSNumber numberWithInt:i]);


--CharlieMiller

NSLog format specifiers:
    
 %@     Object
 %d, %i signed int
 %u     unsigned int
 %f     float/double
 
 %x, %X hexadecimal int
 %o     octal int
 %zu    size_t
 %p     pointer
 %e     float/double (in scientific notation)
 %g     float/double (as %f or %e, depending on value)
 %s     C string (bytes)
 %S     C string (unichar)
 %.*s   Pascal string (requires two arguments, pass pstr[0] as the first, pstr+1 as the second)
 %c     character
 %C     unichar
 
 %lld   long long
 %llu   unsigned long long
 %Lf    long double


"Object" refers to a Cocoa object (responding to -description) or a Core Foundation object. For more, see     man 3 printf. NSLog understands all     printf format specifiers as well as     %@ and     %C.


== Alternatives to NSLog ==

XLog is an alternative if you think the header is too wide or you would like to log the elapsed time from the previous log.

ZNLog  http://codebeach.org/code/show/44

LibComponentLogging is a small library for Objective-C applications on Mac OS X and the iPhone OS which provides log levels, log components for identifying different parts of an application, and an active log level for each log component in order to enable/disable logging for certain parts of an application. Additionally, different logging backends are available, e.g. one which writes to a rotating log file, and one which sends log messages to the Apple System Log facility (ASL). See http://0xc0.de/LibComponentLogging for details.

NSLogger (http://github.com/fpillet/NSLogger) is a high performance logging utility which displays traces emitted by client applications running on Mac OS X or iOS (iPhone OS). It replaces your usual NSLog()-based traces and provides powerful additions like display filtering, image and binary logging, traces buffering, timing information, etc. LibComponentLogging above also has a backend to send its logs to the NSLogger viewer. 

BTTimeLog is a simple Objective-C class I put together that logs the time used by an application. The output includes a logging level, accumulated and differential times, the class and method that made the call, and a user string with arguments. More at http://steveweller.com/articles/speedup/

See NSLogToFile for redirecting NSLog output. 

----

I modified the example such that i is not equal to 0; in that case (on 32-bit Mac OS X, anyway) NSLog would interpret it as the nil object and would not crash. It's still bad form, but the example predicts incorrect behavior.

Do not log a string that you're unsure of its content (for example from the network) as the first argument (format string). Rather, use NSLog(@"%@", str);

