---
layout: page
title: MakingReadRTFDFromFileWorkOnOtherMacs
---

Ok, I have an application that uses multiple .rtf files and switches them through a text view.  I'm using the readRTFDFromFile method to read the files off my HD.  How would I make this work on other macs as well as mine?  Could I send the files to a server and have the application read them from there?  This would be an alternative because I want the application to run without be connected to the internet.

----
I would suggest merely adding these files to your project's resources, which will make them get copied into your .app bundle.

----
Thanks that works fine.
----
How do you get the location that your application is running from?

----

[[NSBundle mainBundle] bundlePath]

Read: 

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/Reference/Reference.html

----

If your app isn't bundled for some reason (CLI app, say) then I think you can use NSProcessInfo to get the app's location.

----
I don't see any way to do that. If you're referring to the     -arguments method, be aware that     argv[0] is *not* a reliable way to find out where your binary is located.

However, NSBundle will still work to find the executable even in an unbundled application, strange as that may be.

----

In an unbundled application, NSBundle assumes the parent directory of the executable to be the bundle.

