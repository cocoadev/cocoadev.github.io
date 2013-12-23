---
layout: page
title: GSFont
---

Describe GSFont here.

You can get a GSFont like this:
struct __GSFont *font = [NSClassFromString(@"WebFontCache") createFontWithFamily:@"Helvetica" traits:2 size:16];

NOTE: You must include WebCore/WebFontCache.h and link the GraphicsServices and WebCore frameworks. Also, WebFontCache.h will not work unless you touch NSObject.h in the WebCore includes folder.

NOTE: Please get the GraphicsServices.h from binutil project (http://developer.berlios.de/projects/iphone-binutils/). It provides more function prototypes than the one in toolchain. I tried  GSFontCreateWithName("Helvetica", kGSFontTraitBold,12); and it works.

----
Okay, got a font object, but it crashes when I do [@"foo" drawAtPoint:CGPointMake(100,100) withFont:font];. Same as when I use GSFontCreateWithName() to get a font.

GSFontCreateWithName() takes three parameters, and will crash if you are only passing the name in. Take a look at <GraphicsServices/GraphicsServices.h> in the toolchain svn trunk for more info. --LucasNewman

Just noticed the "drawMarkupAtPoint" method, which does render text (in Times, looks like).

----
Did you figure it out? I too get a crash when trying to use the font I created with the NSClassFromString.

----
Updated the code; I haven't tried using drawAtPoint, but I could setFont a UITextLabel, AddSubview that, and it worked fine.

----
I tried using the code above, it still crashes when I try to call setFont on a UITextLabel object.

----
I use the following successfully

UITextLabel *_title = [[UITextLabel alloc] initWithFrame: CGRectMake(120.0f, 5.0f, 150.0f, 20.0f)];
[_title setFont:[NSClassFromString(@"WebFontCache") createFontWithFamily:@"Helvetica" traits:2 size:14]];

----
I get the following runtime exception when using any of the above methods.

CGAffineTransformInvert: singular matrix.

