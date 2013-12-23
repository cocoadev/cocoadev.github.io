---
layout: page
title: UndefinedSymbolsWhenCreatingUniversalBinary
---



I am using Xcode 2.4.1 to build a universal binary for an application. In the debug configuration, where the architecture of the project is set to ppc, the application builds without error on my ppc. When I switch to the Release configuration, where the architectures are ppc and i386...I get the following warning/errors:

		/usr/bin/ld: warning /Users/~/Desktop/General/RealEyes/Quartz.framework/Quartz cputype (18, architecture ppc) does not match cputype (7) for specified -arch flag: i386 (file not loaded)
		/usr/bin/ld: Undefined symbols:
		.objc_class_name_PDFDocument
		_PDFViewPageChangedNotification
		.objc_class_name_PDFView
		/Users/~/Desktop/General/RealEyes/build/General/RealEyes.build/Release/General/RealEyes.build/Objects-normal/i386/General/ScriptWindowController.o reference to undefined .objc_class_name_PDFDocument
		/Users/~/Desktop/General/RealEyes/build/General/RealEyes.build/Release/General/RealEyes.build/Objects-normal/i386/General/ScriptWindowController.o reference to undefined _PDFViewPageChangedNotification
		/Users/~/Desktop/General/RealEyes/build/General/RealEyes.build/Release/General/RealEyes.build/Objects-normal/i386/General/MyPDFView.o reference to undefined .objc_class_name_PDFView
		collect2: ld returned 1 exit status
Build failed (1 error, 1 warning)

I have followed the instructions for building Universal Binaries at http://developer.apple.com/documentation/General/MacOSX/Conceptual/universal_binary/ and am otherwise a little bit lost as to what I need to do or what I have forgotten.

Thank you.

----
Somehow you copied Quartz.framework into your project directory, and of course you got a PPC-only version of it. Delete the framework from your project directory and link against the one in /System. Then your SDK setting will take over and grab the Universal version of Quartz.framework from the 10.4u SDK.

----
That did it...Thank you.
