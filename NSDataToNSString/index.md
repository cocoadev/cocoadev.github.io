---
layout: page
title: NSDataToNSString
---



What's the best way to convert the contents of an NSData object to an NSString (other than using its     description method)?

----

If the NSData object contains asc characters then do this:

    NSString *stringFromASC = [NSString stringWithCString:[ascData bytes] length:[ascData length]];

If the NSData object contains unichar characters then do this: http://goo.gl/OeSCu

    NSString *stringFromUnichar = [NSString stringWithCharacters:[unicharData bytes] length:[unicharData length] / sizeof(unichar)];

Be aware that **neither of the above methods** stop at a null character. The next two examples are the safest way to convert raw data into a string object. If you want to have the string terminate at a null character, you have to scan the data first to check for the location of the null character. 

To check for asc termination:

    
    char *ptr, *bytes = (char *)[ascData bytes]; 
    int len;
    len = (ptr = memchr(bytes, nil, len = [ascData length])) ? ptr - bytes : len;
    NSString *stringFromASC = [NSString stringWithCString:bytes length:len];


To check for unichar termination:
    
    int i, length; 
    unichar *c = (unichar *)[unicharData bytes];
    for (i = 0, length = [unicharData length] / sizeof(unichar); i < length && c[i]; i++);
    NSString *stringFromUnichar = [NSString stringWithCharacters:c length:i];


*You can also use the "initWithData:encoding:" NSString method which has a few more options.* <- How does this method handle premature null termination? If the implementation does this properly then     initWithData:encoding: is probably better.

----
Note: [NSString stringWithCString:bytes length:len]; will leak memory, use a non-deprecated function, unless you want to sink like the titanic.

~J

*Ok, it's deprecated... but how will it leak? On another subject, for the record, the line containing     memchr is the scariest bit of programming I've seen all day. Embrace the semicolon! Don't be afraid to put things on more than one line.*

----

How about NSString's

- (id)initWithData:(NSData *)data encoding:(NSStringEncoding)encoding

----
[NSString stringWithCString:bytes length:len] leaks memory on 10.4, believe me.

----
Do you have proof of this? I wrote a simple test app, creating a thousand strings using that method, and     leaks found nothing. When I increased it to ten thousand, the total number of malloced nodes was exactly unchanged from before. So from what I can see, based on tests, it does not leak.

----

    
Leak: 0x04b4c880  size=48       instance of 'NSCFString'
        0xa073a674 0x0001078c 0x17206669 0x6c657328     .s.t..... files(
        0x33333829 0x2073697a 0x6528322e 0x32322920     338) size(2.22) 
        0x00000000 0x00000001 0x00000000 0x00000003     ................
        Call stack: [thread 1b0b]: | 0x0 | start | start | main | NSApplicationMain | -[NSApplication run] | -[NSApplication nextEventMatchingMask:untilDate:inMode:dequeue:] | _DPSNextEvent | BlockUntilNextEventMatchingListInMode | ReceiveNextEventCommon | RunCurrentEventLoopInMode | CFRunLoopRunSpecific | __CFRunLoopRun | __CFRunLoopDoSources0 | __CFSocketPerformV0 | __CFSocketDoCallback | _SocketCallBack | _CFStreamSignalEventSynch | MyCFReadStreamCallback | -[AsyncSocket doCFReadStreamCallback:forStream:] | -[AsyncSocket doBytesAvailable] | -[AsyncSocket completeCurrentRead] | -[CoreController onSocket:didReadData:withTag:] | -[Parser parse:fromBuffer:] | -[Parser handleResponse:] | -[Parser scanCommandBody:withCommand:] | +[NSString stringWithCString:length:] | -[NSPlaceholderString initWithCString:length:] | CFStringCreateWithBytes | __CFStringCreateImmutableFunnel3 | _CFRuntimeCreateInstance | CFAllocatorAllocate 


----
Ok, I got to the root of this. It is some code conversion i am doing from 10.2 to 10.4 and lo and behold there was:

    
NSString *subCommandBody = [NSString stringWithCString:subCommandStart length:length];
char *subCommandCString = (char *) subCommandBody copy] cString];

// We copied subCommandBody so we need to release it.
[subCommandBody release];


In which [subCommandBody release]; was never in place...

----
That's bad code. When you copy     subCommandBody, you get a *completely separate* object. So releasing     subCommandBody doesn't release the copy, and will in fact delete the original string. The only reason this *does* work is because copying an immutable object is usually implemented as retaining it. In any case, the     stringWithCString:length: makes a unique (autoreleased) copy of the string, so you don't need to copy it again. Finally, as you probably know (as you said code conversion), you should use the     cStringUsingEncoding: method if you are only going to be using 10.4, or     UTF8String otherwise, since UTF8 is a superset of ASCII. --[[JediKnil
    
NSString *subCommandBody = [NSString stringWithUTF8String:subCommandStart];
const char *subCommandCString = [subCommandBody UTF8String];

// If we want to use subCommandBody later, we should retain it here
// If we want to use subCommandCString later, we should copy it into a new C string.
char *copiedCString = calloc(strlen(subCommandCString) + 1, sizeof(char));
strcpy(copiedCString, subCommandCString); // requires <strings.h>

P.S. To answer the original question,     -initWithData:encoding: probably *is* the right way to do this.

----
Ouch, yes, that was bad code. And bad code + bad code = code that works and does not even leak. A miracle!! Anyway, I was just reading this and I can see a connection with the above code

http://www.joelonsoftware.com/articles/ThePerilsofJavaSchools.html

----

Well, I have a new question for those here assembled. No pun intended. Is the NSString method

    initWithData: ascData encoding: NSASCIIStringEncoding

more or less efficient (or the same as) the class method

    stringWithCString:[ascData bytes] length:[ascData length]

assuming ascData is my NSDataObject containing only ASCII? It seems the first option sends fewer messages. I don't need to retain whatever is returned, since the result is going to be appended to a mutable string instance variable. This is going to happen lots of times, confined within a single while loop. So is it better in this case to deal with a lot of autoreleased objects or to go through
the alloc-init-release? I could create a special autorelease pool just for this loop.

----
Pardon my naivet�, but why on earth would you care about the efficiency ?  Have you profiled an application and found that a lot of time is spent initializing strings from data?  Are you doing this millions of times per second in tight loops ?  I suspect that initWithData:ascData encoding:NSASCIIStringEncoding actually ends up calling [ascData bytes] anyway, so I'd bet there is no difference in performance.  If (and its a big if) you have performance problems, there is always const char *myCString = (char *)[ascData bytes];

----
Yes, I admit I can answer this question myself by some simple profiling. No, not millions of times per second, but a lot. Just something to think about at dinner over here in GMT-8. Thanks for the C casting reminder - I'm going to try it out. (This actually is something I could realistically contemplate using in the context of a tight loop.) And seriously, your naivet� is nothing when compared with mine.;-)

