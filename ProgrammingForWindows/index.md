---
layout: page
title: ProgrammingForWindows
---

I have a question:

Currently, I am programming my app in Cocoa and obviously it's only available for Mac OS X. However, many people have emailed me and requested a Windows version. This, to me, seems like an overwhelming task (which it probably is).

What would be the best way to rewrite a pure Objective-C application for Windows? Would I use Visual Studio (C#? C++? Basic?) or anything else? I know that it would require a complete rewrite, because even if I figured out how to use GNUStep, it's still just open source software that's not even complete or standard, and I want a complete Windows standard app, as Cocoa is Mac standard.

Anyone have any suggestions or thoughts? -- KevinWojniak

----

The typical way to do these is to have your core application logic in a portable format (C / C++), and have the UI stuff be in the native toolkit (Cocoa / MFC)

----

That's what I would recommend, but so often so much of the program is UI code, so it may not make the app very much easier to port.  Could a possibility be for you to hire a Windows programmer to port it for you?

- FranciscoTolmasky

I'm not a huge fan of RealBasic, but depending on the application, that might be a viable cross-platform toolkit.

----

Yes I had previously worked in REALbasic, and was seriously considering rewriting my app in REALbasic but to me that's like a huge step backwards, even though it builds Windows. I'm not sure exactly what I'll do now. -- KevinWojniak

----

I agree with the earlier recommendation: make sure your core logic is C (or maybe C++ if you trust your C++ compilers) and build the UI using native tools (Cocoa / MFC or whatever). For "difficult to port UI code", factor it as best you can so you can share most of the implementation between platforms. That way you're taking advantage of the Mac-ness or Windows-ness of the platform you're deploying on, which is best for users.

I'm not a big fan of "cross platform" toolkits, since they're all "least common denominator". But if you must use a "write once, debug everywhere" strategy, stick with Java. You can write your core logic in Java, and still use Cocoa for your UI on Mac -- then fall back to whatever "least common denominator" features Java provides you. In theory you'll have an app that can run on Windows, Linux, etc., but still be "best of breed" on Mac.

-- MikeTrent

