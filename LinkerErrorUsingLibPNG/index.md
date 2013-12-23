---
layout: page
title: LinkerErrorUsingLibPNG
---



I'm transitioning an OpenGL game I'm writing from using targa images to PNGs. I tried using NSImage to load the pngs, but unfortunately, NSImage premultiplies the alpha channel which makes it useless for me. For what it's worth, I do have some quicktime code which will load pngs, but I find Quicktime to be a horrible mess, on the order of Win32 and I'd like to avoid it as much as possible.

That said, I figured libPNG is the way to go. However, I couldn't find libPNG on my system. Which seems odd since OS X natively supports PNG files quite well.

So, I downloaded the source for libPNG and built it to a static library, libpng.a, and included it in a lightweight GLUT sample project for the purpose of integrating it into my texture manager code. 

I include the static lib by dragging the lib to my Frameworks entry in Xcode and I added -lpng to the linker flags.

For my GLUT app, which is just a testbed, it linked and works beautifully. it loads the PNG images, OpenGL accepts the bytes, and everybody's happy.

However, when I tried to integrate it into my game, in the same manner as for my testbed, I get linker errors saying a particular symbol ( just one ) conflicts with a symbol in AppKit. This seems odd to me since GLUT apps link against Cocoa which I assume references AppKit.

This also seems odd that AppKit would have libpng in it already.

So, I guess it comes down to two approaches to solve this:
a) I could somehow link it differently
b) I could somehow use the libpng in AppKit

But I'm not certain how to approach either one. Any ideas?

Thanks
--ShamylZakariya

here's the output:

    
Ld /Users/zakariya/Projects/Legion/build/Legion.app/Contents/MacOS/Legion
    cd /Users/zakariya/Projects/Legion
    /usr/bin/g++-3.3 -o /Users/zakariya/Projects/Legion/build/Legion.app/Contents/MacOS/Legion -L/Users/zakariya/Projects/Legion/build -Llib -L/Users/zakariya/Projects/Legion/lib -F/Users/zakariya/Projects/Legion/build -Flib -filelist /Users/zakariya/Projects/Legion/build/Legion.build/Legion.build/Objects-normal/Legion.LinkFileList -framework Cocoa -framework Carbon -framework GLUT -framework OpenGL -lGLEW -lode -lftgl -lfreetype -framework PANSICore -lz -lpng -arch ppc -prebind -Wl,-no_arch_warnings -lmx
ld: multiple definitions of symbol _png_get_uint_31
/System/Library/Frameworks/AppKit.framework/Versions/C/AppKit(single module) definition of _png_get_uint_31
lib/libpng.a(pngrutil.o) definition of _png_get_uint_31 in section (__TEXT,__text)


----

Remove libpng.a from your project (but leave the headers). It should Just Work. Using CoreGraphics is another option. You can probably make NSImage/NSImageRep do what you want, though: take a look at the NSBitmapImageRep class.

Hmm, in the very first paragraph of the documentation, I found: "If you need to work with unpremultiplied data, you should use Quartz, specifically CGImageCreate with kCGImageAlphaLast." Sounds like it's worth looking into CoreGraphics.

----

I ended up using Quicktime to load the images -- which had the added benefit of allowing me to use PNG images and to resize images before accessing their bytes. I noticed the CoreImage stuff while trolling the documentation. It looks promising -- particularly since Quicktime generally gives off a Bad Smell. 

That said, I expect to leave it as is, until I ( someday ) port to SDL and use SDL_Image for my loading.

--ShamylZakariya

----

Out of curiosity, Why would you say "Quicktime generally gives off a Bad Smell"?

Because it is C or you just perceive it as antiquated because of it's maturity?

**My impression of QuickTime has always been that it could be rather... shall we say surprising, to those who didn't have the chance of programming in Classic MacOS. I think that's one of the reasons that it took so long for QuickTime integration in OS X to be actually, you know, smooth. I quite like it... but it still scares me. -- RobRix**

----

LOL, I hear you, I was just curious.

