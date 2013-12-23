---
layout: page
title: ArchivingObjects
---

When you use NSKeyedArchiver encodeRootObject to archive a whole graph of objects, does it figure out the graph itself by following pointers, or do you need to specify the links yourself in your encodeWithCoder method?

e.g. If I have

    
@interface WikiWikiWeb : NSObject <NSCoding> {
    NSMutableArray * firstObject;
    NSDictionary * secondObject;
}



in my WikiWikiWeb.h, do I need 

    

(void)encodeWithCoder:(NSCoder *)coder
{
    [coder encodeObject:firstObject forKey:@"One"];
    [coder encodeObject:secondobject forKey:"@"Two"];
}


in WikiWikiWeb.m so that it knows how to form the graph, or will it figure it out from the class definitions?

Thanks.

ArthurLewis

----

You need to implement the NSCoding protocol, so yes. -- FinlayDobbie

----

Hi, firstly, this is a *brilliant* site. I have been software mashing for nearly 20 years and lately I have discovered ObjC and Cocoa, it has to be the *best* thing to develop with without actually getting to use Smalltalk. Sigh!

Anyway, to the point...today I tried to pickle an NSInvocation through NSArchiver and try as I might, I couldn't make it happen. I wanted to sent a pickled invocation back to my main thread via NSConnection. The connection works fine, as does the program because, thanks to http://cocoa.mamasam.com/ (I found a 'dodge' (casting NSInvocation to unsigned and setting it as the msgid), for which I was very happy to find.

Basically,


* Can you archive an NSInvocation, forgetting all issues about argument lifetimes etc etc I would be passing simple types  or things guaranteed to remain alive.
*If so, how? :-)


Thanks in advance,
Sean Charles
http://www.bumpybibbers.com

----

From The Fine Manual:

*Note: NSInvocation conforms to the NSCoding protocol, but only supports coding by an NSPortCoder. NSInvocation does not support archiving.*

----

I put together a tutorial on NSKeyedArchiver/NSKeyedUnarchiver and NSCoding:

http://cocoadevcentral.com/articles/000084.php  -- ScottStevenson

