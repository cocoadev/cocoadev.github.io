---
layout: page
title: EnhancingBuiltInImageImport
---

Subclassing NSImageRep to import unknown picture formats is reasonably well documented. In my icon program I wanted to have the ability to read Windows XP icons. They normally have the .ico file format extension, and as such are already recognized by Cocoa programs as images. However, the built-in image import does not take into account the transparency channel.

As I am interested in the transparency information, I decided I needed to write my own image import routine. The ico file format is documented in MSDN, so that wasn't a problem.

The problem was, that as ico's are already recognized and imported by NSBitmapImageRep, which registers very early before my NSImageRep, it takes precedence over my routine. My solution was to unregister NSBitmapImageRep just before adding my import class to the registry. When I added my import, I readded NSBitmapImageRep, which then registered all file types except for the ico import.

    

+ (void)load
{
    [NSImageRep unregisterImageRepClass:[NSBitmapImageRep class]];
    [NSImageRep registerImageRepClass:self];
    [NSImageRep registerImageRepClass:[NSBitmapImageRep class]];
}

+ (NSArray*)imageUnfilteredFileTypes
{
    return [NSArray arrayWithObject:@"ico"];
}

+ (NSArray*)imageUnfilteredPasteboardTypes
{
    return [self imageUnfilteredFileTypes];
}

+ (BOOL)canInitWithData:(NSData *)data
{
    return YES;
}



If anyone is interested in the complete image import code for XP icons, send me a mail.

-- DavidRemahl

----

Is the newly registered image type only available in your application or is it available across all applications in the system? -ChrisMeyer

----

It'd have to be a filter service or in a framework for that, AFAIK. -- RobRix

----

Any ideas if filter services are still supported? It seems that SimpleFilterService example doesn't work even if I change the extension to .service. Are there any ways to extend built-in image formats? QuickTime component perhaps?

