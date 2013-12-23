---
layout: page
title: DealingWithFiles
---



Are there any tutorials available on file handling?
I want to read a specific text file into memory and read various strings from it.

p.s.  Is there a more appropriate place to ask such simple questions?  I don't want to clutter this site with too many "beginner" level questions if the level of this forum is targeted above that.

Thanks!

----

This is actually pretty easy to learn just with the documentation. I know, I know, more 'read the docs'. But it really is true here. To get started, look at NSString in the docs. Check out its - (BOOL)writeToFile:(NSString *)path atomically:(BOOL)flagatomically: method. Once you read this (and the associated 'read'), you'll slap yourself in the forehead. ;-)

NSString - http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSString.html
NSMutableString - http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSMutableString.html

 -- Oh, and one more thing: though NSString has a lot of methods to sling strings around and do all kinds of neat stuff with them, it doesn't do everything. NSScanner is a more advanced tool for more advanced needs. All this should pretty much solve a lot of string manipulation needs. Of course, depending on what you're doing, consider looking into using the built-in PLIST stuff. NSDictionary's writeToFile automatically creates PLIST files out of some pretty complicated data structures (provided everything is NSCoding compliant). 

  In short, this is not a terribly difficult thing to do. It just requires some reading to do it well.

-JNozzi

----

Thank you for pointing me in the right direction.  

I don't, however, see a corresponding 'read' method for NSString.
You're quite right, though � more straight forward than I had thought.

*    stringWithContentsOfFile will do the trick.*

----

You're absolutely right - I was quite busy this morning! ;-) There is no 'read' method, as with the code someone kindly posted below, you normally create an object with the contents of a file for things such as NSString, NSDictionary, NSArray, NSData, and all their variants. - JNozzi

----

Sample code...
    
NSString *pathToFile = [@"~/Desktop/aFile.txt" stringByExpandingTildeInPath];
NSString *contentsOfFile = [NSString stringWithContentsOfFile:pathToFile];

/* contentsOfFile now contains the text in aFile.txt */


----
Hello. I figured this was a reasonable thread to join to ask this question: I have written a program that does NOT use the cocoa document architecture, since it's primarily a QuickTime movie player. (It's part of a simulator, but that's beside the point.) I made a file extension (".sim" -- the program is pretty specialized so it is unlikely to be used by anyone but our small group, ever, so I can get away with this sort of extension hijacking...) and the program knows (via XCode) to save and open the data files within the main program. However, although double-clicking one of the data files successfully launches the app, the app does not read the file successfully. I don't know how to determine programmatically that the app was launched by double-clicking (or opening) a readable data file, thus instructing the app to load that data file. The File > Open command works fine, but it apparently isn't invoked when the user launches the app (or even brings it to front) by double-clicking a data file. The data file, by the way, is just a list of NSStrings. The program is designed to load the last data file used when it's launched, so I need to test for a launch that resulted from double-clicking the data file. I'm sure it's obvious, but I can't find the answer in Apple's XCode documentation.

Thanks for pointing me right!
-dan

----

you want to use NSApplication's     - (BOOL)application:(NSApplication *)theApplication openFile:(NSString *)filename delegate message.

----
Thanks! -dan
OK... More than thanks. That was very straightforward. I'm still fairly new to Obj-C (and modern programming in general), so in case anyone else reads this who needs a step-by-step, as I usually do, what you do is choose a delegate in Interface Builder for the File's Owner. This can be done by control-dragging a connection from File's Owner to the object you wish to have handle the file reading bits. Make the connection for delegate in the inspector palate (you can bring this up with Apple-2). Save your IB changes, then return to XCode. In the now-delegate object for the NSApp object (you just connected it to File's Owner), add this method:

    - (BOOL)application:(NSApplication *)theApplication openFile:(NSString *)filename

Write whatever code you need (I simply used this method to write     filename to my defaults file so that the applicationDidFinishLaunching method found this filename and opened it. Make sure you include a     return YES; to the method we just wrote so that the program knows you've chosen a file already.

I know this is a long-winded restatement of the above response to my question, but for new guys like me, I need to see this level of detail most of the time. Thanks again to the anonymous responder who got me on the right track!
-dan

