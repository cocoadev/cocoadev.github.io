---
layout: page
title: UsingUnixLibraries
---



There's an open source C++ media library I would like to use in my application; it's designed for linux, but the author stated it should work under OSX. With that in mind, how do I incorporate the library into my project? I assume I would have to build the library and link it to the app; what steps would I need to take in General/XCode to accomplish this? This library will not be installed on the end user's system; do I need to package it with the application somehow, or will everything be built into the executable?

This library is licensed under the GNU Lesser General Public License. I know that if I were using the "proper" GPL I would have to release my source under the GPL as well, but the LGPL is confusing me-- can I pick whatever license I choose, or simply choose not to release the source? Will there be any issues with licensing if I need to package the library with the application?

----

The GNU LGPL allows you to use the library in your application, even if you don't GPL, LGPL or open-source it.

If you want to change the library and distribute the changed version, the modified library and those changes have to be released under the LGPL, but applications using the library can still use any license.

Simply put the LGPL is a GPL that only affects the library, not applications that use the library.

Take a look at General/StaticVsDynamicLinking for some instructions on how to link UNIX-libraries with your application.

--General/TheoHultberg/Iconara

However, with an LGPL'ed library, you'll *have* to link dynamically. Basically, the license says that your user must be able to swap in another (better) version of the library and have everything still work. You'll have to distribute all changes you make to the library itself, too.

Example: take any of the Unreal Tournament titles, which use SDL (a cross-platform media library). Unreal dynamically links to SDL, and I have to be able to take the SDL source code, compile a library from it, and put it into my Unreal bundle to use it. The distributor of UT has to give me his SDL source code if he has modified it; if he hasn't modified it, it's normally sufficient for him to point me to the original SDL home page (though he would have to supply me with the source if the original site closed down).

----
Static linking is discouraged. If you give me a static library, and there's not a dynamic version around, I can link to it just fine (as long as my product isn't dynamic). And there are workarounds (though they should be unnecessary) to include static libraries in a project.

--General/JediKnil

----
It is nearly impossible to link an entire application statically. You have to include at least some dynamic libraries, such as libSystem. It's possible to do it all statically, but you leave yourself open to massive breakage, and it's not easy. Linking against individual static libraries is simple, though.

see also General/ApplicationLinkingIssues, with related information.

----
If you want to include pre-built libriaries in the package of your application to link to, you can. Just toss them in /Contents/Frameworks/ and then use install_name_tool and otool to make sure that your executable points to @executable_path/../Frameworks/libraryname. (otool will tell you what they are, install_name_tool will let you change them)

General/GormanChristian
