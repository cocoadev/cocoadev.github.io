---
layout: page
title: NiblessPDFCreation
---

Hi Everyone,
I'm attempting to write a small commandline program that will generate small PDFs for me.  I figured it might be worth a go to write this in PyObjC.  If you guys think it'd be easier to do this in normal ObjC I'd be more than happy to.  I've gotten things to a point where it will spit out a PDF the size I want, but unfortunately it's an empty PDF.  I was wondering if you guys had any idea why it won't draw anything.  Here's what I've got so far:
    
#!/usr/bin/env pythonw

import objc
import sys
from Foundation import *
from AppKit import *
from PyObjCTools import AppHelper

class AppDelegate (NSObject):
  def applicationDidFinishLaunching_(self, aNotification):
    print "Hello, World!"

def main():
  app = NSApplication.sharedApplication()
  # we must keep a reference to the delegate object ourselves,
  # NSApp.setDelegate_() doesn't retain it. A local variable is
  # enough here.
  delegate = AppDelegate.alloc().init()
  NSApp().setDelegate_(delegate)

  fughettaFont = NSFont.fontWithName_size_(u"Fughetta", 40)

  # Make the bezier path for our thing.
  notePath = NSBezierPath.bezierPath()

  # Move to where we need to be and add our note.
  notePath.moveToPoint_(NSMakePoint(25, 25))

  # Glyph number 192 is the closed dot.
  notePath.appendBezierPathWithGlyph_inFont_(192, fughettaFont)

  # Create the unfortunately necessary window
  win = NSWindow.alloc()
  drawing_rect = NSMakeRect(0, 0, 50, 50)
  win.initWithContentRect_styleMask_backing_defer_(drawing_rect, 15, 0, 0)

  # Create our NSView in which to draw our stave.
  drawing_view = NSView.alloc().initWithFrame_(drawing_rect)

  # Add the view to the window.
  win.setContentView_(drawing_view)
  drawing_view.lockFocus()

  # Draw our dot at 25 25
  NSColor.blackColor().set()
  notePath.fill()

  # Save to a pdf
  data = drawing_view.dataWithPDFInsideRect_(drawing_rect)
  data.writeToFile_atomically_(u"test.pdf", 1)

if __name__ == '__main__' : main()


----
This is a good example of why you should avoid invoking     lockFocus on an NSView.

What's happening is that     dataWithPDFInsideRect simply asks the view to redraw itself and uses that to create the PDF. It is not getting the drawing which currently exists in the window's backing store, it's just having the view refresh itself. Since this is just a naked NSView with no custom code, its     drawRect: does nothing.

Another problem is that you probably need to get AppKit started before you do other things. My code spit out errors unless I invoked     [NSApplication sharedApplication] before I started manipulating images and such.

You can just subclass NSView and put your drawing code there. If you don't like that, you can draw into an image, then use NSImageView to get that in PDF form. However, this will get you a rasterized image, not a vector image, and that's probably less than ideal.

----
Ahh!  Thanks for that!  Subclassing the NSView fixed everything.  Thanks a bunch!

