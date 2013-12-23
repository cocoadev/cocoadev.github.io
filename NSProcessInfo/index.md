---
layout: page
title: NSProcessInfo
---

[http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSProcessInfo.html]

A FoundationKit class containing various low-level information about your process and the system it's executing on, such as environment variables, arguments, OS type, host name and so on. You access your process' shared instance of NSProcessInfo with     [NSProcessInfo processInfo].

It can also be used to generate strings guaranteed to be unique on your network with     [[NSProcessInfo processInfo] globallyUniqueString]  <- also see IDentifiers

