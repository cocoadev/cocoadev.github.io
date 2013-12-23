---
layout: page
title: JavaBridge
---

The Cocoa Developer Documentation page on Apple's web site: 
   developer.apple.com/techpubs/macosx/Cocoa/CocoaTopics.html
isted Java bridge as a legacy programming topics.  Is it dead?  Will it be updated?  Can we rely on this core piece of technology to be there in the future? 

The JavaBridge is what makes it possible to access Java classes from Cocoa-applications in other languages. It's an extraordinary piece of software, though not well documented. There are projects going on to create similar bridges for PythonLang, PerlLang (CamelBones) and RubyLang.

How does one go about UsingCompiledJavaCodeInObjectiveC?

Incorporating Java into an Objective-C project in Project Builder/Xcode: XcodeObjCJavaBridge.
----
I'm curious as to what Apple's intentions with the Java bridge are. The documentation for it hasn't been updated in ages. This seems to indicate that the bridge will be going away in the future. However, IIRC Apple's own WebObjects still uses the bridge, as do multiple third-party applications (see TrailBlazer). Will Apple break these third-party applications, possibly reserving the bridge for its own internal use? http://goo.gl/OeSCu

As much as I like Objective-C, there are just a ton of useful Java libraries out there that have no direct Objective-C equivalent (see the Apache Jakarta project). I am considering using the Java bridge for an upcoming project, however I don't want to be left out in the cold if Apple decides to remove the bridge.

One thought that occurred to me is creating an open-souce version of the Java bridge. I haven't looked into it yet so I have no idea of the level of effort it would require or if it's even feasible at all. Other languages with an open-souce Objective-C bridge seem to be doing well (PyObjC in particular). Any thoughts? Comments?

-EricWang

----

Have a look at Aquataxx, a game that has network play and uses Cocoa completely for its UI and Java Networking.  http://buzzlabs.org/  Source code included in archive.  Good luck with the NIB files, however; it's difficult to figure out what the errors are; looks like they ripped out much of the Java support (or I just haven't kept up with how IB works). 

Steve

----

Apple's Java Bridge is deprecated in favor of JNI. JNI stands for Java Native Interface. JNI is a Java standard that defines how Java code can call native routines, and how C code can call Java routines. The Java bridge is a very nice thing, but JNI is very close is functionality, and quite easy to wrapper into a ObjC class if you prefer.

You can learn more about JNI here

http://java.sun.com/j2se/1.4.2/docs/guide/jni/

--TimHart

*where's it say that?*

Oh, come on, the JavaBridge is way more sofisticated than JNI, Apple would not deprecate the JavaBridge in favor of something so basic as JNI, that would absolute madness. JNI is not very nice to work with, and it's stone-age compared to the JavaBridge.

----

13 years of development in C, C++, Objective-C, Java, and other languages writing everything from low-level terminal code ( credit card swipe machines) to large n-tiered applications with literally 1,000,000 lines of code ( no, not all by myself ) tells me that Apple's deprecation of the Java Bridge is anything but absolute madness. It was a very wise decision to create it in the first place. It served an important need. A roadblock was hit, and intelligent people realized it was better to abandon the attempt and move on.

Here are some facts:


*Apple's Java Bridge is legacy technology. It is no longer supported by Apple.
*Tools related to Apple's Java Bridge are no longer supported by Apple or available from Apple - such as Wrapit.
*Apple's Java Bridge is buggy as hell. 
*Objective-C is weakly typed. Java is strongly typed. Automatically resolving the issues that result from this over a bridge are problematic at best.
*Java uses constructors to initialize objects. Objective-C uses initializers that may return completely different instances. Automatically resolving this difference across the Bridge has also been problematic for Apple ( or anyone )
*Objective-C's messaging system is incompatible with Java's method invocation system. They work very similarly most of the time, but the flexibility that messaging gives you for delegation simply does not exist in Java.
*Objective-C's selector's also do not relate one-to-one with Java's method names. It is impossible to guarantee automatic translation from a selector to a method call, or visa versa.


The language incompatibilities between Java and Objective-C are what eventually led to the Java Bridge's downfall. Any one of those incompatibilities can be resolved for the specific case, but writing generic code to make the right decision for every possible case just can't be done.

For a simple illustration: Java's overloading behavior resolves method invocation at compile time. So if you have

    
public class SomeClass
{
    public void foo(Object a){/*...*/}
    public void foo(MyClass a){/*...*/}

    public static void main(int argc, String argv[]){
        SomeClass someClass = new SomeClass();
        Object a = new MyClass();
        MyClass b = new MyClass();

        someClass.foo(a);//the compiler will choose the first implementation above, 
                                   //because the variable a's declared type is Object, not MyClass
        someClass.foo(b);//here, the compiler will choose the second implementation.
}


...how do you code your generic Java Bridge code to reproduce the functionality? Remember that Objective-C doesn't do overloading. Objective-C also doesn't store ANY information regarding the declared type of a variable - only the declared type of a parameter, and the runtime type of any object. Your Obj-C to Java bridge code can certainly intercept the invocation, but there is no way to use Objective-C to get the same results in the generic case:

    

@implementation

-(id) someMethod
{
    Class SomeClass = NSClassFromString(@"SomeClass");
    id someClass = [ [ SomeClass alloc ] init ];

    Class aClass = NSClassFromString(@"MyClass");
    id a = [ [ aClass alloc ] init ];
    MyClass *b = [ [aClass alloc] init ];

    //Remember Objective-C does no compile-time resolution at all,
     //so how do you differentiate between the two distinct java methods?
    [ someClass foo:a ];//which implementation of foo should get called?
    [ someClass foo:b ];//at runtime, the declared type of 'b' is unknown.

    //this will work, but it's ugly. Imagine what this would look like with a method taking 3 arguments...
    [ someClass foo:b 
             withArgumentTypes:@encode(typeof(b)), nil];
}
@end



The above isn't a complete example, and there are solutions. You can use the Objective-C compiler directive '@encode' to encode the type of the argument, but now every single method call has to have it hanging off the end.

Don't misunderstand me. Apple's Java Bridge was a Very Nice Thing(tm), and provided Apple with a great means of attracting developers and giving developers easy access to a lot of functionality on both sides of the development community.

I hold high regard for the development team that created the Java bridge. I've developed code to bridge runtimes before - across platforms even. Making a Cobol application running on VAX VMS talk to a Fort� application* running on Windows NT - and vice versa- is no easy task. Mine was a specific need. Coding a system to work in the generic case is very difficult indeed.

The JNI specification for Java is low level - granted. But there is nothing 'stone-age' about it. It operates at a level that is guaranteed to be compatible with the platform's native ABI. That's a lot more than simply C, C++, and Objective-C. It's also possible to call native routines from Java. That's any routine in any dynamically loadable library that conforms to the platforms' native ABI. The JNI specification has been updated and modified through and including JDK 1.5.

The JNI is at an appropriate level to use in order to solve the specific case. Every time.

So in a nutshell, the Java Bridge was very nice, but impossible to maintain in a form that would be absolutely bug-free, because there were critical portions of the code where the developer had no choice but to make an educated guess. The mis-match between Objective-C and Java's typing systems, messaging systems, and initialization systems cannot be perfectly resolved. The JNI will solve every problem correctly. JNI is developed and supported by Sun on many platforms. Apple's JVM supports JNI thoroughly. What possible reason would Apple have for continuing to support a technology that they could never get 100% correct, especially now that the transition from OS9 to OS X is 'complete' with a much more solid development environment, and JNI has a proven track record.

*Fort� is a defunct application development environment

--TimHart

*I see your point, 'stone-age' was (as it seems you guessed) meant to say that it's very low-level, which I think is not appropriate for this kind of thing. Cocoa-Java is supposed to 'just work', and using JNI is not a very Apple thing to do, and it's not something that 'just works'. It seems as though the JavaBridge has huge problems, and maybe the solution is JNI, but hopefully at the base of a new high-level JavaBridge. Cocoa-Java would be doomed if client programmers had to resort to JNI to get things done. --Theo*


*There's several simple generic ways that example could have been done,  a nicer looking way Apple would have just had to add one method more method to JavaBridge Proxy Object.*

    

@implementation

-(id) someMethod
{
    Class SomeClass = NSClassFromString(@"SomeClass");
    id someClass = [ [ SomeClass alloc ] init ];

    Class aClass = NSClassFromString(@"MyClass");
    id a = [ [ aClass alloc ] init ];
    //this doesn't look too ugly, seems quite reasonable since we are working with a proxy to statically typed runtime.
    [a setJavaCast:NSClassFromString(@"SuperClass");
    id b= [ [aClass alloc] init ];
    
    [ someClass foo:a ];//which implementation of foo should get called?
    [ someClass foo:b ];//at runtime, the declared type of 'b' is unknown.

}
@end



*And your ugly way (but using java signatures would be more appropriate) would have been a be pretty straight forward to implement too as having a label on the second argument guarantees that you are not going to have a collision with a java method, allowing you to parse it in forward invocation and thus you don't need to put the argument on each method (performance penalty would be a downside to this). And a third option you could have a perform selector type method that is just for invoke java methods when you need to cast to a specific type, and pass in a selector and java signature.*

*On the documentation it's clear that the bridget stuff is legacy and calling objective-C from java is a lot of effort. However the other way around, doesn't look too bad, and when I look at the example JNI sample code on Apple's site for calling Java from a core foundation program, I can't help  but think that it's just a little less than what apple is wrapping in  NSJavaVirtualMachine (Which isn't flagged as deprecated in it's public header as of Panther at least) and is giving a very object oriented way and much less ugly was to accessing java objects than JNI directly. If I personally needed to access a java api from objective-C, and NSJavaVirtualMachine was just magically gone, I'd just wrap JNI code in objective-C something similar with proxy objects and forward invocation, because it would be a lot less code that using JNI directly. --JayTuley*

----


The above does not address one important question: Why do you say that the java bridge is deprecated? Is this documented somewhere?

Are you claiming the cocoa-java is now deprecated? I've certainly not seen evidence of that.
----

Quoted from the following URL:

http://developer.apple.com/referencelibrary/LegacyTechnologies/idxCocoa-date.html

*As Cocoa and Mac OS X evolve, the APIs and technologies they encompass change to meet the needs of users and developers. As part of this evolution, less efficient features, interfaces, and programming techniques are deprecated or retired in favor of newer ones. Apple makes these changes only when deemed absolutely necessary. A technology identified in the ADC Reference Library as deprecated has been superseded and may become unsupported in the future. A technology identified as unsupported is no longer available from Apple for use by developers. Legacy documents help developers understand legacy technologies, identify replacements, and update their products to run on current Apple platforms.*

The Java bridge is listed at the bottom of this list. Cocoa-Java is not. I do not know the current relationship between the two, although I do believe that at one time Cocoa-Java used the Java Bridge. Perhaps it still does. Perhaps it now uses JNI. *AFAIK, all Cocoa-Java classes are bridget-generated and use the Java Bridge (as the bridge-loading key in InfoPlist leads me to think). -- l0ne aka EmanueleVulcano*

--TimHart

----

Cocoa-Java is certainly deprecated; Apple's Cocoa-Java Integration documentation (http://developer.apple.com/documentation/Cocoa/Conceptual/LanguageIntegration/index.html) has this:

*"The Cocoa-Java API is deprecated in Mac OS X version 10.4 and later. You should use the Objective-C API instead..."*

and

*"Features added to Cocoa in Mac OS X versions later than 10.4 will not be added to the Cocoa-Java programming interface. Therefore, you should develop Cocoa applications using Objective-C to take advantage of existing and upcoming Cocoa features."*

----

There used to be a general-purpose JavaBridge that would allow users to create their own custom bridges. You can still (last I checked, anyway) see remnants of this in the /Developer folder. There are old example bridge files still lying around, but even they say in their comments that the bridging tools have died.

*Sure, but they're still in the process of dying. I got the Java bridge to work in CallingObjCFromJava, although this was almost completely undocumented. Now I have to learn JNI... --JediKnil*

----

I wrote a Java program to generate Objective C header files for the corresponding Java class. This is available at:
http://homepage.mac.com/jamiec/JavaBridge/BridgeWeaver.tar.gz

An example project is available at:
http://homepage.mac.com/jamiec/JavaBridge/JavaHack.tar.gz
This Objective C sample project does some simple stuff with a Vector and pops up a JFrame with a JButton inside of it.

Jamie Cho

----

There is a new, Apple independent, Java-Cocoa bridge project Rococoa - http://rococoa.dev.java.net
Needs more classes, but at least it promises to be there even if Apple removes their deprecated
bridge.

Raymond Martin

----

CocoaJava has been permanently removed in 10.6 (Snow Leopard) so apps that rely on it will crash on launch and log some errors to system.log, viewable in Console.app. It shouldn't come as a huge surprise, but now it's officially time to move on...

----

Cyberduck (a CocoaJava app) still works under 10.6 (Snow Leopard). It now uses Rococoa instead of the old JavaBridge.

