---
layout: page
title: XcodeObjCJavaBridge
---

Effective Use of the Objective-C/Java Bridge
----
Author: General/AlainODea

Last Update: October 20, 2004 (General/EvanSchoenberg)
----
Getting and using Java objects in Objective-C Cocoa projects is easy once you figure it out. Unfortunately, Apple's documentation on using the Objective-C/Java Bridge is not only scarce, it is also inaccurate. The goal of my hint is to provide you with the ability to develop a project including your own Objective-C code, Java library classes, and your own Java classes.

The primary advantage of the Java bridge is access to functionality provided by the General/JavaClassLibrary that is either non-existent or inconvenient in Cocoa. 

I have verified that the same steps work for any Objective-C project type. However, in the interest of providing a simple working demo, my explanation will use a Foundation tool.

Follow these steps to see the Objective-C/Java Bridge in action:

*Open Project Builder/Xcode
*Create a new Foundation project called     General/HelloBridge
*Create a new Pure Java Package target called
    General/JavaClasses
*Add the General/JavaClasses product to the resources build step of the General/HelloJava target
*Create a new Java class called
    General/HelloBridge.java
Content:
    
public class General/HelloBridge {
   private String string = "Hello";

   public void setString(String string) {
      this.string = string;
   }

   public String getString() {
      return this.string;
   }

   public void printString() {
      System.out.println(this.string);
   }
}

*Add
    General/HelloBridge.java
To build phase
    Sources 
In target
    General/JavaClasses
*Create a new empty file called
    General/JavaInterfaces.h
And add it to target
    General/HelloBridge
Content:
    
// Provide Objective-C interfaces for the Java classes
// Not only good practice, it provides Code Sense
@interface java_util_Vector : General/NSObject
{}
- (void)add:(id)anObject;
- (id)get:(int)index;
@end

@interface General/HelloBridge : General/NSObject
{}
- (void)setString:(General/NSString *)string;
- (General/NSString *)getString;
- (void)printString;
@end

*Modify
    main.m
Content:
    
#import <Foundation/Foundation.h>
#import "General/JavaInterfaces.h" 
int main (int argc, const char * argv[]) {
   General/NSAutoreleasePool * pool = General/[[NSAutoreleasePool alloc] init];
   
   // Load the Java VM
   id vm = General/NSJavaSetupVirtualMachine();
   
   // Start the Java class loader
   // no need to provide &vm, since we just got it above
   General/NSJavaClassesFromPath(General/[NSArray arrayWithObject:General/[[NSBundle mainBundle] pathForResource:@"General/JavaClasses" ofType:@"jar"]], nil, YES, nil);
   
   // Load a new instance of the java.util.Vector Java class into an Objective-C pointer
   java_util_Vector * vector = General/NSJavaObjectNamedInPath(@"java.util.Vector", nil);
   [vector add:@"one item!"];
   General/NSLog(@"item 1=%@",[vector get:0]);
   [vector release];
   
   // Load a new instance of our custom General/HelloBridge Java class into an Objective-C pointer
   General/HelloBridge * hello = General/NSJavaObjectNamedInPath(@"General/HelloBridge", nil);
   General/NSLog(@"item 1=%@",[hello getString]);
   [hello setString:@"Test"];
   General/NSLog(@"item 1=%@",[hello getString]);
   [hello printString];
   [hello release];
   
   [pool release];
   return 0;
}

*Build the target     General/JavaClasses *Build and Run the target     General/HelloBridge *The standard output should show the two timestamped General/ObjC General/NSLog entries and one plain General/JaVA System.out.println() entry


----

Problems:

*Your code crashes with the call to General/NSJavaClassesFromPath() when I follow your instructions.
*How does the application product find the General/HelloBridge.class without adding it to the target in some way?
*There was also a redundant instruction about adding General/HelloBridge.java to the General/JavaClasses target.
*The string constant in a *.java file should not have the "@" symbol for an General/NSString constant.


I've made some changes which fixed the problem for me. � General/BrentGulanowski

----
Relevant Links:

General/JaVA General/JavaBridge General/JavaVersusObjectiveC General/CallingObjCFromJava

----

I'm having another problem with using the Java Bridge via Objective-C. I've got a jar that I've compiled with Eclipse that causes errors like this:

    
Java General/HotSpot(TM) Client VM warning: Attempt to guard stack yellow zone failed.
Java General/HotSpot(TM) Client VM warning: Attempt to guard stack red zone failed.
2004-09-20 18:50:18.785 General/HelloBridge[29049] General/NSJavaVirtualMachine
2004-09-20 18:50:18.786 General/HelloBridge[29049] <General/NSJavaVirtualMachine: 0x328720>
com.apple.misc.General/BundleClassLoader@40e45a: while trying to define archiveresource:file:/Volumes/Clarke/Users/brentgulanowski/Programming/PB builds/General/JavaClasses.jar/+/General/HelloBridge.class, (null), got throwable: java.lang.General/UnsupportedClassVersionError: Unsupported major.minor version 48.0
java.lang.General/UnsupportedClassVersionError: Unsupported major.minor version 48.0
	at java.lang.General/ClassLoader.defineClass0(Native Method)
	at java.lang.General/ClassLoader.defineClass(General/ClassLoader.java:488)
	at java.lang.General/ClassLoader.defineClass(General/ClassLoader.java:423)
	at com.apple.misc.General/BundleClassLoader.getClassesFromLocations(General/BundleClassLoader.java:158)
	at com.apple.misc.General/BundleClassLoader.getClassesFromResourceLocator(General/BundleClassLoader.java:115)


General/JavaClasses is a jar just like in the tutorial above. But that jar references another jar called JXTA.jar (http://www.jxta.org). I try to access a class from JXTA in my test Java class and that leads to the error. I would really like some help, if possible. Any Java gurus around? � General/BrentGulanowski

----

Brent, I've run into the same problem.  It seems that the General/NSJavaVirtualMachine class insists upon loading Java 1.3.1, even though a later version may be installed... and if your .jar file requires a later version (say, 1.4.2, which is the current version as of this writing), then it has a major.minor version of 48.0 and gets rejected by the General/ClassLoader.

I haven't found a workaround yet; Apple indicates here ( http://developer.apple.com/documentation/Java/Reference/Java14JavaDict/General/KeysValues/chapter_3_section_1.html ) that General/JVMVersion is needed in the .plist to specify a version other than 1.3.1, but this only seems to work for a double-clickable app which is loading a jar, not for a Cocoa program loading the General/NSJavaVirtualMachine to load the jar. � General/EvanSchoenberg

Evan, I have succeeded in getting the JVM 1.4 to load properly.

I've found that if you want to use code that requires 1.4, you have to change at least one other of two settings and maybe both of them, but that these settings are (as far as I can find) only exposed in legacy targets. Next to the JVM version setting (Legacy target->Settings->Java Compiler Settings->Target VM Version:) is a Source Version pop up -- I set both of these to 1.4, in addition to the Info.plist entry. I don't know which of these made the difference; haven't had time to explore further. I haven't found analogous settings in Native targets. � General/BrentGulanowski

----

There is a simple solution for General/XCode and native targets.
Open your info.plist file and in the main dict add
    
<key>Java</key>
<dict>
	<key>General/JVMVersion</key>		
	<string>1.4*</string>
</dict>

it will work
Pierre Oleo

----

Everyone seems to want to call Java code from Objective-C (with good reason), but how do you go about calling Objective-C code (that I wrote myself) from Java? The only thing I can find is http://developer.apple.com/documentation/Cocoa/Conceptual/Legacy/General/JavaBridge/General/JavaBridge.5.html, which doesn't seem very helpful. None of the instructions in there apply to Xcode. The reason I want to do this? Using a Java framework, I am trying to find out what the inheritance chain of an Objective-C class is (by name), and there's really no way to do that...as far as I can see, I'm stuck!

--General/JediKnil

----

General/JediKnil, you might want to have a look at this page: http://www.montagetech.com/developer_bridget.html. I agree with you that Apple should update the General/JavaBridge and provide some better documentation for it. Browsing through the cocoa mailing lists, there's clearly interest in the technology. I'm still holding out faint hope that Apple will make Java a true peer of Objective-C in 10.4, although it's probably just wishful thinking on my part. I'm currently looking into the feasibility of writing an open-source version of the General/JavaBridge in my spare time (see the General/JavaBridge page for more info). Let me know if you have any thoughts on the subject.

-General/EricWang

----

Thanks Eric, that really helped me...almost. I can build a library in objc, create a java object, and *attempt* to call a method. No good. The Java Bridge throws me back with a link error:

    General/AppKitJava: uncaught exception java/lang/General/UnsatisfiedLinkError (className)

I looked at the stub files generated by bridget, and I don't see any obvious mistakes. Of course, they're written using macros in macros in macros and lots of crowded syntax, so I don't understand *much* of it. It's just the methods -- I don't see what I'm doing different than the Montage people...

Thanks again for your first link. If you have any suggestions...

[EDIT: Never mind :) I was using the wrong version of the library. Thanks again for the link. I put up step-by-step instructions on the page General/CallingObjCFromJava.]

--General/JediKnil

----

Hey folks- I'm having some fun with this Java Bridge stuff.  First of all, I was receiving warning messages:

Java General/HotSpot(TM) Client VM warning: Attempt to guard stack yellow zone failed.
Java General/HotSpot(TM) Client VM warning: Attempt to guard stack red zone failed.

so, I changed my info.plist to declare the JVM as 1.4.  That results in the following warning messages:

Java General/HotSpot(TM) Client VM warning: Attempt to protect stack guard pages failed.
Java General/HotSpot(TM) Client VM warning: Attempt to deallocate stack guard pages failed.

no idea what these error messages are (I have tried Googling them but no luck).  However, I am able to load Java objects and access functions just fine if I am doing simple set/get operations.  If I try something more complicated like using a Java object to connect to a database, then things get messed up.  I have my java-accessing-code inside exception handling and the exception always comes back as:

java/lang/General/OutOfMemoryError

Anyone have any ideas?  I would really like to abstract my database connection/ querying code into Java classes...

Thanks for the help
-Ryan

----

Sorry Ryan, no ideas here...I don't know much about General/HotSpot. In general, I think the top example is pretty messy...see http://cocoadevcentral.com/articles/000024.php for a slightly easier example. Also notice the format for multiple-argument call to a Java class, as well as two alternative constructer formats, one which supports arguments:
    
// Adds the string "test" to the vector at position 1
[myJavaVector add:1 :@"test"];

// New vector, returns id, could also be alloc-init
General/[NSClassFromString(@"java.util.Vector") new];

// Varargs constructor
General/[NSClassFromString(@"java.util.Vector") newWithSignature:@"(Ljava/util/Collection;)", someCollection, nil];
// This uses all parameters as arguments to the Java constructor with the specified
// method signature (look at the General/CocoaDevCentral link above for more info).

--General/JediKnil

----

Using information from this page and several others I think I've figured out how to incorporate 3rd party Java JAR files into a Cocoa project. It took experimenting with a Cocoa-Java project and carrying settings over to the straight Java project. In the end it worked yet I was left with some questions...

- warning messages still appear re: the Java Hotspot VM and its guard pages, they do not appear in the Cocoa-Java app
- what's with the different UI for editing Info.plist depending on what type of project you create??

anyways, the details are on my weblog here:
http://www.compsigh.com/entry.jsp?id=cocoajava.text

--emh

----

By the way, DTS is recommending that new development uses JNI rather than the bridge. -- General/FinlayDobbie

----

Is that referring to new development accessing Objective-C from Java? I haven't seen any indication that the bridge is going away for Java use by Objective-C developers. **Apple, however, reccomends not to use it anymore and use JNI in its place.**

*

But how is JNI supposed to help when trying to use Java objects from Objective-C ????

*

----
The JNI specification covers calling Java methods from C. Java runtimes include native libraries for interfacing with the java runtime. Look for  jni.h in the include directory. You'll need to link with jvm.lib (I beleive) in order to use the functions described in jni.h

The complete JNI tutorial can be found here:

http://java.sun.com/docs/books/tutorial/native1.1/index.html

Also look at this portion of the JNI tuturial:

http://java.sun.com/docs/books/tutorial/native1.1/implementing/index.html

--General/TimHart

----

This page is a great resource. Of course it wouldn't be necessary if Apple gave the proper documentation for this stuff...but I digress.

I am getting the error:

    
org/osaf/caldav4j/CalDAV4JException (Unsupported major.minor version 49.0)


Which we all know means that Java 1.5 is required but the JVM is running 1.4 (or less.)

So I added:
    

<key>Java</key>
<dict>
	<key>General/JVMVersion</key>		
	<string>1.5</string>
</dict> 


to my Info.plist. I also tried 1.4+, 1.4*. Nothing works. Does anyone have a reccomendation for getting Java 1.5 working in a Cocoa app?

Thanks!!!

General/BobbyRullo
**UPDATE: ** I figured it out. I needed an additional key:

    
    <key>General/NSJavaNeeded</key>
    <string>YES</string>


----

If you want to load the JVM 1.5 in a foundation shell tool, I found you can add this environment variable:

    
  export JAVA_JVM_VERSION=1.5


This is referenced here ( http://developer.apple.com/technotes/tn2005/tn2147.html ) under the section "Requesting a specific J2SE version" - General/DavidThorpe
