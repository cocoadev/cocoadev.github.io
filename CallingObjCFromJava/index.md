---
layout: page
title: CallingObjCFromJava
---



Created by JediKnil

----

During one of my projects, it became necessary to find out the class hierarchy of an ObjectiveC object. The catch? The object had to be identified by name, and it *didn't* have to be part of the Cocoa framework. Because of this, I had to create an Objective C class that could find Objective C class definitions (the     Class type), and return usable data to my JaVA class.

In the original ProjectBuilder, it was a lot easier. There was built-in support, and the documentation (in the Legacy section on Apple's site, all about WebObjects) actually applied. In XCode, a whole ton of this was broken, probably because Apple didn't see the need to fix it. By now, it seems like JNI is the way to go; however, this may still be of interest to someone.

After many failed attempts (involving JNI, private JavaBridge header files, and the enigmatic     .jobs file), I was finally able to get my bridged class to work (thanks to EricWang). So, here's how you do it using a dynamic library in XCode 1.5:

----

1. Create a new "Empty Project" and name it whatever you like (maybe something descriptive?)

2. Add a Target (Project -> New Target) with the "Cocoa -> Dynamic Library" type.

3. Switch into the Target Settings' Expert View and set the following keys and values. If you don't have the specified key, add it to the list by clicking the little plus button. Note that my computer's copy of jni.h seemed to be missing, so you will have to provide your own path

*Header Search Paths --- /Developer/Java/Headers */path/to/jni/dot/h*
*Library Search Paths --- /usr/lib/java
*Product Name --- lib*Whatever*
*BRIDGE_OBJC --- YES


3a. If you want to embed your library in your final product, add "-seg1addr 0xC0000000" to "Other Linker Flags" and set "Installation Path" to "@executable_path/../Resources" (like when embedding a framework). This prevents memory collisions between your library and your main product. If you have multiple libraries, try different numbers -- the same offset will end up with another collision. Check out Apple's prebinding documentation ( http://developer.apple.com/documentation/Performance/Conceptual/LaunchTime/Tasks/Prebinding.html ) for available, valid values to use with -seg1addr.

4. Write your Objective C classes. One note of warning -- parametized     init methods don't seem to work right, at least not for me. Create class methods to do the same thing. Example:
    
+ (OCClassDescription *)newWithClassName:(NSString *)name;

instead of
    
- (id)initWithClassName:(NSString *)name;


In my case, newWithClassName: actually *calls* initWithClassName:. This is fine, just don't expose your init: functions in your     .jobs file. More about this later.

Other than that, just write it like a normal class. Normal, unparametized     init seems to work fine.

5. Create a header file that has the same name as your library. In it, put #imports for all your bridged classes. This is the easiest step in the entire project.

6. Create a     .jobs file. This has become much harder since the WrapIt tool has disappeared from DeveloperTools (I don't know when). Here's a sample file. Anything in italics must be replaced by your code.

    
# This is a comment.

# The name tag is mandatory. It determines the names of the resulting files,
# and should be the same as the .jobs filename (without the extension, of course)
name *project*

# There can only be one header tag per jobs file. This file is used to find the classes to
# be extracted. This is why you had to group all your class headers together with imports
header *projectheader.h*

# Adds one of the standard Cocoa jobs files to your project.
# Normally ObjCJava.jobs is the only one you need, but you
# might also need the AppKit (Foundation is included).
# /Developer/Java/Jobs
import ObjCJava.jobs

# This is placed at the top of all generated stub and init code.
# It translates to "#import *aheader.h*"
# Usually you could just use *projectheader.h* for this.
stub-import *aheader.h*

# The selector directive is used to specifiy any unusual method names.
# Normally only the first piece of the name (before the first colon) is used.
selector
     *-instance:method:that:takes:aLot:of:args* = *longInstanceMethod*
# end of selector section

# The long Java name is just to show that
# 1. You need to include the package name
# 2. The class name doesn't have to be the same.
class *MyObjCClass* = *my.package.TheObjCClassIWantToExpose*
     *+classMethod*
     *+classMethodThatTakesAnArgument:*
     *-instanceMethod*
     *-instance:method:that:takes:aLot:of:args:*
# Remember that in Java, there are no pointers. This means a function
# can only return one value at a time. Try to write intermediary functions
# if you have to return multiple values. See Apple's documentation on this
# (link after code)

#    constructor *initWithStringOrSomething:*
# For some reason the constructor directive doesn't work right, nor
# does directly exposing any method that starts with init. Bridget
# makes these private (because they should be called by constructors),
# but I get type mismatches whenever I try it. Just use factory methods
# (*+newWithString*) or a no-argument *-init*

     @{
          *public static final int three = 3; // Java code to be added at somewhere*
          *// in the bridged class. Often used to implement enums*
     @}

     @top{
          *import my.other.package.*; // Java code to be added before the bridged class.*
          *// Often used to add import statements*
     @}

     @end{
          *private class SecretClass { /* My secret class */ }*
          *// Java code to be added after the bridged class. No use I can think of.*
     @}
# end of the class

# Exactly the same as the class directive, except it generates interfaces
# from formal protocols.
protocol *MyProtocol* = *my.package.JavaInterfaceVersionOfMyProtocol*
     *-specifiedFunction:*
# Same syntax for method definitions
# end of the protocol

# Used for turning typedefs into regular types. Can also be used to turn
# structs into classes, but that's a bit harder.
type 
     *MyTypedefNumber* = *int*
     *SomeFloatingValue* = *double*
# end of the type section

# Used for turning ObjC classes into normal Java classes.
# The two identifiers after the "using" keyword refer to conversion functions:
# This is beyond my expertise, like morphing structs.
map
     *MyObjCMapOrSomething* = *java.util.Map* using *MyMapToAJavaMap* *JavaMapToMyMap*
# end of the map section

# The specified zero-argument functions will be called before and after initialization.
# I don't know why you would want these...maybe to print something to the console?
preinit-callout *callBeforeInit*
postinit-callout *callAfterInit*


Whew! Now that you've finished, we can actually build the library -- almost. Please post any questions about     .jobs files on this page...after looking at Apple's Legacy documentation at http://developer.apple.com/documentation/Cocoa/Conceptual/Legacy/JavaBridge/JavaBridge.5.html

7. Open up Terminal and     cd to the same directory as your     .jobs file (which should be in the same directory as your header files). Type in the following, replacing anything in italics..

    bridget -java -stub -init -o *output/directory/path* *bridgefile*.jobs

You should now have Java classes generated for all your classes. If you used packages, your classes will be in a normal Java class tree (*my/package/name/MyObjCClass.java*)

8. There should also be stub files for your classes, in the form of     .m code, as well as a file called "*project*-init.m". Add these to your project and make sure they are included in the build as well.

9. Add the     /usr/lib/java/libObjCJava.dylib library to your project. (Thanks to Mark Allan for helping me catch this crucial step). If you don't know how to get to the hidden     /usr folder, just click on this link: file:///usr/lib/java and drag the     libObjCJava.dylib *alias* into your project.

10. Finally! Click the build button. Don't worry if it takes a while -- this is normal. Bridging takes a lot of work! There might also be warnings that pop up in the bridget-generated files; you can ignore these as far as I know (the one I get says `__ret' might be used uninitialized in this function).

11. That's it! If you put your library in one of the standard search paths (usually     /usr/lib/java or     /usr/local/lib/java), and include your Java class in your final product, it should just...work! Isn't Cocoa awesome when you don't stop to read the documentation?

You're probably feeling pretty self-satisfied right now. But isn't it kind of annoying when you put your product up for download, and some idiot sends you an email saying the program crashed because it couldn't find your Java library? Let me tell you how to get around that...

If you skipped step 3a above, you should probably go back and do it...

1. Open your final product project and drag your bridged library into your files list.

2. If Xcode is functioning properly, your library should show up in your Frameworks & Libraries build phase. Drag your library from the file list on the left (not the build phase file list) into the Resources section of your project. Your library should now be in two different build phases: Resources and Frameworks & Libraries.

3. The way your bridged Java class loads its Objective C counterpart is through NSRuntime. If you check Apple's NSRuntime documentation ( http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Java/Classes/NSRuntime.html ), you'll see that the Resources directory is automatically searched for libraries, right? So you're done, right?

Wrong. If you call librarySearchPaths(), you'll see that the Resources directory NSRuntime looks at doesn't exist, and indeed *cannot* exist in any valid bundle (they left Contents/ out of the path). Hopefully Apple will fix this at some point, but for now, go back to your     .jobs file and add the following (bold) lines to the bottom of **each** of your class definitions. Example:

    
class *MyObjCClass* = *my.package.TheObjCClassIWantToExpose*
    *-instanceMethod*
**    @{
        static {
            NSRuntime.addPathToLibrarySearchPaths(NSBundle.mainBundle().resourcePath());
            NSRuntime.loadLibrary("ObjCJava");
            NSRuntime.loadLibrary("*project*");
        }
    @}**


    "*project*" should, of course, be replaced by the name of your project. Run     bridget again, making sure to remember the     -java option and adding     -noload to your flags. Now when your class is initialized, your library *will* (which means "should") be loaded properly. Don't try to modify the generated java file in any way unless you're sure you know what you're doing...

Just kidding. Play around with it, by all means. Just don't complain to me that your file isn't working unless you've tried a fresh copy (and don't forget to add the above code to your     .jobs file).

4. Build and run. It should be working now!

----
**March 16, 2005:** Updated a whole lot of stuff to work with my version of XCode, which suddenly started acting weird... :) --JediKnil

**March 17, 2005:** Suddenly found a much easier way to fix library search paths!
----

Please help, I can't get this to work and desperately need it for a large project I've written!  Could you possibly upload your source code of this sample please?  I find it a lot easier to follow instructions if I've got access to the full sample code.  Anyway, I've followed your instructions to the letter (except the bits specific to my own code), here's the error I'm getting
    
Linking /Users/mark/Desktop/testingBridge/build/libMJA.dylib
Undefined symbols:
_JAVAThrow
_NSIdToJavaHandle
__JavaStringToNSString
__NSAllocateObjcObjectForJavaHandle
__NSAssociateJavaObjectNoRetain
__NSDisassociateJavaObjectNoRelease
__NSJavaStubEnter
__NSJavaStubExit
__NSRaiseExceptionInJava
___NSRealObjcClassForId
_NSSetJavaClassAndConstructorsForObjcClass
internal link edit command failed
Build failed for target "libMJA" using build style "Development"


Could you also copy a personal reply via http://www.markallan.co.uk/contact.php please?  Thanks a million.
----
Ouch! I completely forgot one step: adding the     /usr/lib/java/libObjCJava.dylib library to your project! This should fix everything (I tried pulling it and got all of these errors). Thanks for helping me catch this. Also, remember that the JavaBridge *is* deprecated, which means Apple might (but probably won't) remove support for it at any time. I (and probably you as well) will eventually have to rewrite our libraries using JNI. And as per your request, my ObjC introspection code is temporarily available for download (without the build directory) at http://www.geocities.com/jediknil/belkadan/objcjava.sit --JediKnil

