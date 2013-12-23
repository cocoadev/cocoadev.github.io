---
layout: page
title: NSScriptObjectSpecifier
---



Working on *AppleScriptabilty* of my Colloquy and ChatCore project I was in dire need of a way to take an object specifier AEDesc and resolve it. AEResolve didn't help, as it only works with applications that have dozens of assessors installed, not based on key-value coding.

I needed a way to pass object specifiers to an AppleScript subroutine and get object specifiers from a script's result.

After hours of painful searches I found nothing helpful. Time for *class-dump* I said.

Doing a dump of the Foundation framework (*class-dump -s -C NSScriptObjectSpecifier /System/Library/Frameworks/Foundation.framework/Foundation*), I found my  answer!

    
@interface NSScriptObjectSpecifier (NSPrivate)
+ (id) _objectSpecifierFromDescriptor:(NSAppleEventDescriptor *) descriptor inCommandConstructionContext:(id) context;
- (NSAppleEventDescriptor *) _asDescriptor;
@end


A quick test on my script proved my joyous find. I don't know what *inCommandConstructionContext* is, and passing nil to it worked.

This really needs to be public, methinks. �TimothyHatcher

As of Leopard it is public:     +[NSScriptObjectSpecifier objectSpecifierWithDescriptor:] and     -[NSScriptObjectSpecifier descriptor]. Slowly, slowly, Apple exposes to us the APIs they know are needed to write working code... :) ---WimLewis

