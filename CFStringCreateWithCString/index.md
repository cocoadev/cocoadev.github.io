---
layout: page
title: CFStringCreateWithCString
---

http://developer.apple.com/documentation/CoreFoundation/Reference/CFStringRef/Reference/chapter_2.1_section_2.html#//apple_ref/doc/uid/20001211-DontLinkChapterID_1-F11124
 
and add any comments here...

some simple examples http://www.carbondev.com/site/?page=CStrings+

*Is this any safer than StringWithCString?*

Yep.  The problem with methods like     -[NSString stringWithCString:] is that they don't specify encoding.  This function does take an encoding argument.

