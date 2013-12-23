---
layout: page
title: QuartzComposerCompileLinkerError
---

I'm getting an odd linker error:

    
Ld /Users/jerry_wong/dev/squeakie/build/Debug/squeakie.saver/Contents/MacOS/squeakie normal i386
    cd /Users/jerry_wong/dev/squeakie
    /Developer/usr/bin/gcc-4.0 -o /Users/jerry_wong/dev/squeakie/build/Debug/squeakie.saver/Contents/MacOS/squeakie -L/Users/jerry_wong/dev/squeakie/build/Debug -F/Users/jerry_wong/dev/squeakie/build/Debug -filelist /Users/jerry_wong/dev/squeakie/build/squeakie.build/Debug/squeakie.build/Objects-normal/i386/squeakie.LinkFileList -framework Cocoa -framework ScreenSaver -arch i386 -bundle -mmacosx-version-min=10.5 -isysroot /Developer/SDKs/MacOSX10.5.sdk
Undefined symbols:
  ".objc_class_name_QCCompositionLayer", referenced from:
      literal-pointer@__OBJC@__cls_refs@QCCompositionLayer in squeakieView.o
ld: symbol(s) not found
collect2: ld returned 1 exit status


I have included Quartz/Quartz.h, and the compiler doesn't complain about an undeclared class when declaring the QCCompositionLayer pointer, but for the life of me I cannot figure out why the linker is having problems with this.

Below is the relevant chunk of code

    
#import <Cocoa/Cocoa.h>
#import <Quartz/Quartz.h>

@implementation squeakieView

... some stuff here

- (void)startAnimation
{
    [super startAnimation];
	
	QCCompositionLayer		*root_layer = nil;
	root_layer = [QCCompositionLayer compositionLayerWithFile:
           [[NSBundle mainBundle] pathForResource:@"purpleclouds"
                                           ofType:@"qtz"]];
										   
	[self setLayer:root_layer];
	[self setWantsLayer: YES];
}


Thanks in advance!

----

I don't see     -framework Quartz anywhere in that     ld step, only     -framework Cocoa and     -framework ScreenSaver.

