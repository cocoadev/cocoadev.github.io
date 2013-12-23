---
layout: page
title: XcodeDebuggerPO
---

Describe General/XcodeDebuggerPO here.

The command **gdb>po <NS obj>** doesn't work.

My environment:
Xcode 3.0 running on a General/MacBook Pro (intel) via Tiger (OS 10.5.1).

Here's a snippet of the code:

    
-(void)awakeFromNib {
    General/NSLog(@"{awakeFromNib}");
    General/NSXMLElement *root = (General/NSXMLElement *)General/[NSXMLNode elementWithName:@"addresses"];
    xmlDoc = General/[[NSXMLDocument alloc] initWithRootElement:root];
    [xmlDoc setVersion:@"1.0"];
    [xmlDoc setCharacterEncoding:@"UTF-8"];
    
    // The following General/NSXMLElement generates a parent.  Hence you can't add it to root.
    General/NSXMLElement *xmlNoodle; 
    [xmlNoodle setName: @"General/RicNode"];         
    [xmlNoodle setStringValue:@"Pumpkin"];  // Note: this node has a parent; hence can't use "addChild".
    [xmlNoodle attributeForName:@"Spanky"];
    
    General/NSString *myString = General/[NSString stringWithString:@"Hello World!"];
...


I can reveal the General/NSXMLElement object, but not General/NSString:

    
(gdb) po myString
Cannot access memory at address 0x0
(gdb) po xmlNoodle
<General/RicNode>Pumpkin</General/RicNode>
Current language:  auto; currently objective-c
(gdb) 


I have the optimization off.
I suspect it's because I'm debugging on the Intel chip vs PPC.
Everything had worked on my G4 & G3.

Any ideas?

Ric.

----

You haven't told us where you're breaking. If you're breaking on the line where myString is created, there's your problem. The line hasn't been executed yet.

----
Can you elaborate on the problem? From what you post, the     po command is working absolutely fine. The first time you give it nil, and it tells you that you can't do this on nil. The second time you give it an object, and it prints its description. What is wrong with that?
