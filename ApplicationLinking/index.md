---
layout: page
title: ApplicationLinking
---

ApplicationLinking, or just linking, is the final stage in the build process for binaries built with C and C-like languages, as well as other languages (such as many Pascal and assembly variants) which use a C-like build process. The linking stage is when external libraries are included in the binary. Linking can also refer to dynamic or runtime linking, which happens when a program is launched and is when dynamic libraries are loaded into the process's address space.

This page will give an overview of what linking is, how it works on OS X, and how it relates to Cocoa.

If you've come here because you're having a problem with dynamic libraries or frameworks, this page will give you a good overview of the mechanisms involved and will help you to solve your problem. If you're looking for a page which discusses specific, commonly-seen issues with linking, see ApplicationLinkingIssues.

The contents of this page are geared towards Mac OS X development. Much of the page will apply to other UNIX systems, and I'll try to mark OS X specific information when I can, but I may forget about it, so exercise caution.

----

**Introduction**

Any act of programming on almost any modern OS involves external libraries. Even a simple "hello world" application on OS X will link against     /usr/lib/libSystem.dylib, as well as     /usr/lib/crt1.o. External libraries allow you to easily reuse code written by others. In this case, you're reusing Apple's code which allows you to bootstrap your application (for     crt0.o) or use standard C functions and interact with the system (    libSystem.dylib).

There are two types of libraries, static and dynamic. Static libraries end in     .a or     .o (technically a     .o is not really a library at all, but it can be treated as such), whereas dynamic libraries end in     .dylib*.

Note that frameworks are just dynamic libraries with a funny directory structure. For the most part, whatever is said about dynamic libraries also applies to frameworks.

**Static vs Dynamic**

Static libraries get copied into your binary during the build. This is not a normal file copy, but rather a specialized copy that moves the actual machine code into the newly-built program. This means that the actual library itself does not need to be present for the program to run.

Dynamic libraries do not get copied. When you link against a dynamic library, the linker adds references to the external library to your program, but it does not add actual code. This means that the dynamic library has to exist on a computer for the program to work, and it has to be in a place where the dynamic linker can find it.

**How to Link**

To link against a library using Xcode, simply drag the library into your project like any other file. This has the effect of changing your linker flags to correspond to the library. If you drag in     /path/to/libfoo.dylib (or     .a), this will simply add     -L/path/to -lfoo to your linker flags. Frameworks will add     -F/path/to -framework foo.

There are some odd consequences of this implementation. One is that in order to be usable, libraries (but not frameworks) *must* have filenames that start with     lib. Another is that if you have a static and dynamic library of the same name in the same location, you will *always* link against the dynamic library, even if you actually added the static library to your project.

**Runtime**

If you're linking against a static library, then all the exciting parts are over. It's buried inside your binary's machine code, and that's it. But with dynamic libraries, since they're not copied, life gets more interesting when you actually run the application.

Runtime library loading is handled on OS X by the     dyld program. It's responsible for finding, loading, and linking dynamic libraries into a running program.

Unlike many OSes, OS X does not have a search path for the dynamic linker**. This means that you can't simply put a dynamic library in some "standard" location and have     dyld find it, because there is no standard location. Instead, OS X embeds an "install name" inside each dynamic library. This install name is the path to where the library can be found when     dyld needs to load it. When you build an application that links against a dynamic library, this install name is copied into the application binary. When the application runs, the copied install name is then used to locate the library or framework.

How about an example? You can get a library's install name by using the     otool -D command. Let's look at Cocoa:

    
$ otool -D /System/Library/Frameworks/Cocoa.framework/Cocoa 
/System/Library/Frameworks/Cocoa.framework/Cocoa:
/System/Library/Frameworks/Cocoa.framework/Versions/A/Cocoa


The first line of the output is the file we're actually inspecting, and the second name is the install path. They're different because I actually looked at a symbolic link to the binary, not the binary itself.

How about an application that uses Cocoa? We can look at all of the copied install names by using     otool -L:

    
$ otool -L /Applications/Adium.app/Contents/MacOS/Adium 
/Applications/Adium.app/Contents/MacOS/Adium:
        @executable_path/../Frameworks/AIUtilities.framework/Versions/A/AIUtilities (compatibility version 1.0.0, current version 1.0.0)
        @executable_path/../Frameworks/AIHyperlinks.framework/Versions/A/AIHyperlinks (compatibility version 1.0.0, current version 1.0.0)
        /System/Library/Frameworks/Cocoa.framework/Versions/A/Cocoa (compatibility version 1.0.0, current version 11.0.0)
        @executable_path/../Frameworks/Adium.framework/Versions/A/Adium (compatibility version 1.0.0, current version 1.0.0)
        /System/Library/Frameworks/Carbon.framework/Versions/A/Carbon (compatibility version 2.0.0, current version 128.0.0)
        /System/Library/Frameworks/ExceptionHandling.framework/Versions/A/ExceptionHandling (compatibility version 1.0.0, current version 4.9.0)
        /System/Library/Frameworks/SystemConfiguration.framework/Versions/A/SystemConfiguration (compatibility version 1.0.0, current version 1.0.0)
        /System/Library/Frameworks/AddressBook.framework/Versions/A/AddressBook (compatibility version 1.0.0, current version 483.0.0)
        @executable_path/../Frameworks/Growl-WithInstaller.framework/Versions/A/Growl-WithInstaller (compatibility version 1.0.0, current version 1.0.0)
        /usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 88.1.2)


That's a lot of frameworks! But you can see Cocoa right in there, the third library down, with the same path as above. This means that if you moved     Cocoa.framework,     dyld would no longer be able to find it, and any application that uses it would no longer be able to launch. This is obviously bad, so don't do that.

What's with all those     @executable_path lines? Well, let's say you have a third-party framework, or perhaps a framework you built yourself, which you want to use in your application. That framework has to be present on any system where your application is expected to run. You could create an installer which installs the framework to a well-know location, but installers are bad and so is cluttering up the user's system. 

The answer is to copy the framework into your application itself. Since a     .app is really a directory structure, there's plenty of room for that. But a problem presents itself: the path to the framework is embedded in the app, and the framework must be located there. But we don't know where the app is going to be when the user runs it, so how can we embed this path when we create the application?

The answer to *that* is to use     @executable_path. When     dyld sees that in an install name, it replaces it with the path to the enclosing directory of the program that's loading the framework. In this example, it'll evaluate to     /Applications/Adium.app/Contents/MacOS. The entire path to, say,     Adium.framework will then be     /Applications/Adium.app/Contents/MacOS/../Frameworks/Adium.framework/Versions/A/Adium, which is correct.

Note that     @executable_path always resolves to the application's path, so you can't use it to embed a framework inside a bundle. If you can require Tiger, you can use     @loader_path instead. If you need to support pre-Tiger, your life becomes much harder, and solutions to that are beyond the scope of this page.

For Leopard you can also use     @rpath and specify runpaths with the -rpath linker flag.
For example:
    
cc -o libfoo.dylib -install_name @rpath/libfoo.dylib ....
cc -o main ... -Wl,-rpath,/opt/foo/lib -Wl,-rpath,/home/pogma/lib -Wl,-rpath,/usr/local/foo/lib

When main is run, the dynamic linker will search for libfoo.dylib in the paths specified.

**Changing the Install Name**

Sometimes you'll have a situation where you have a framework that's designed to be installed somewhere special, but you want to make it embedded in your app. Maybe you don't have the source code, or maybe you just don't feel like rebuilding it, so you want to change its install name in place. Maybe you have an application with a bad library path in it, and you want to fix it without rebuilding it.

The     install_name_tool command allows you to change both a library or framework's install name, as well as the copied install names stored in an application, library, or framework. Use the     -id flag to change a library's install name, and use the     -change flag to change a copied install name stored in an external binary.

**Troubleshooting**

Common linker problems are "symbol not found" during compiles, and "image not found" during execution. If the former appears, it usually means that you need a library but you're not actually linking against it. The latter means that     dyld couldn't find a dynamic library or framework that your application needs. Since the necessary code isn't present, your app can't be launched. Make sure that the library is actually where     dyld expects it to be.

For more in-depth troubleshooting, see ApplicationLinkingIssues.

----

*     .dylib is an OS X-ism. Other OSes use different extensions. For example, Linux uses     .so, and Windows uses the infamous     .dll.

** Technically,     dyld does have a search path, defined in the     DYLD_FRAMEWORK_PATH and     DYLD_LIBRARY_PATH variables. However, these are empty on OS X by default, so they rarely matter.

