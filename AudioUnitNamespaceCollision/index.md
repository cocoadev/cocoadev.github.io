---
layout: page
title: AudioUnitNamespaceCollision
---

*The problem begins this way, but is later revealed to be a simple namespace collision... read and become wise....*

I think that my developer tools have kicked the bucket. I am not sure, perhaps I got a virus! Strange errors keep popping up, not when compiling, but at runtime. For example, suddenly my categories do not work. The compiler see's the additions, and does not complain,     NSAssert([ClassName instancesRespondToSelector:theSel], @"...") passes, but     [instance respondsToSelector:theSel] returns NO. NSNotification center does not want to deliver my notifications. Here is the most recent mystery stack trace:

#0	0x9086be80 in _objc_trap

#1	0x9086be14 in _objc_fatal

#2	0x908629c0 in class_initialize

#3	0x90861524 in _class_lookupMethodAndLoadCache

#4	0x90861298 in objc_msgSend

#5	0x908629e0 in class_initialize

#6	0x90861524 in _class_lookupMethodAndLoadCache

#7	0x90861298 in objc_msgSend

#8	0x00005760 in -[MainController initializeCore] at MainController.mm:37


MainController.mm 37 is simply     myServer = [[MYServer alloc] initWithData:data];

Normally I would be happy to blame myself for these errors, however, these errors 'popped-up' after I did NO source code editing. They started after creating a new Cocoa project and I migrated the code there. Furthermore, while some of them have been consistent, some only appeared after a reboot. I went to my old project, and lo and behold some of the same problems appeared (I could not test all of them as my prog crashes before I can). Now, I save binary results from all my builds, and the compile right before I copied the source code to the new project WORKS FINE! There were NO source code changes between the builds, only project changes (non-compiler related).

I know this is scant info, but you have to take my word for it that the source is fine, It worked yesterday, and now It fails in mysterious ways. I do not have high hopes, I just want to know if other people have had broken Dev-Tools, and has a system wipe fixed them?

Frustrated P. Frustrato.

----

Normally this kind of stack trace appears when you try to load two classes into an application that have the same name. This can happen if one of the classes is in your app and another is in a framework, or if you load a bundle that has a class with the same name as one in your app or in another bundle that was already loaded, etc. Is this possible at all? What frameworks are you linking against, what frameworks/bundles are you loading dynamically, and what class names are in your app which might conflict?

----

Thank you so much! This was exactly the issue. I had an AudioUnit with some cocoa classes in common with the audio unit host. After uninstalling the audio unit, everything works as it is supposed to. So is there any way to avoid the issue? Renaming all the classes is an option, but will it work? Thanks again!

*Sure - a concept called Name Space. If your name is Frustrated P. Frustrato, consider naming it, "FPFAudioUnit". That's sure to be fairly unique. That's why the NextStep derived Cocoa api prepends its classes with "NS". There're NSButton (Cocoa), JButton (Java), TButton (turbo pascal / Delphi, at least that's the way it was 10 years ago when I used that stuff), etc.*

Renaming the classes will work; you're just not allowed to have two classes with the same name in the same application.

*Just remember to rename the actual class' .h and .m files, then do a global find & replace from the old class name (just the name itself) to the new name. If you instantiated any instances of the class in any NIB files, you'll need to re-import the headers and change everything in the NIB as appropriate to make it use the new class by its new name. Though, in your situation, that would rename the original AudioUnit classes that are part of the frameworks as well, so you'll probably want to do the search normally but the replace manually. The global find/replace window gives you an easy, effective way of doing both.*

**Also check out WhenAreNamespacesNecessary, ChooseYourOwnPrefix, and AlternativesToPrefixing.**

