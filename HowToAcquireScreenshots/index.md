---
layout: page
title: HowToAcquireScreenshots
---

To get a screenshot of your screen try this :
    
Please port to Cocoa


Or in PyObjC:

    
format = "png"

class CocoaScreenshot(NSObject):
	# Extension => Cocoa constant dictionary
	        self.fileRepresentationMapping = {
		       '.png': NSPNGFileType,
		       '.gif': NSGIFFileType,
		       '.jpg': NSJPEGFileType,
	   	       '.jpeg': NSJPEGFileType,
		       '.bmp': NSBMPFileType,
		       '.tif': NSTIFFFileType,
		       '.tiff': NSTIFFFileType,
		       }

		def _getFileRepresentationType(self):
			""" Cocoa filetype representation function to mach the filetype with the _fileRepresentationMapping dictionary"""
			base, ext = os.path.splitext(shotFile)
			return self.fileRepresentationMapping[ext.lower()]

		def screenshot(self):
			""" Cocoa screenshot implementation """
			# Initialize screen frame and allocate image in memory
			rect = NSScreen.mainScreen().frame()
			image = NSImage.alloc().initWithSize_((rect.size.width, rect.size.height))
			# Create a transparent fullsize window
			window = NSWindow.alloc().initWithContentRect_styleMask_backing_defer_(
							rect, 
							NSBorderlessWindowMask, 
							NSBackingStoreNonretained, 
							False)

			view = NSView.alloc().initWithFrame_(rect)
			window.setLevel_(NSScreenSaverWindowLevel + 100)
			window.setHasShadow_(False)
			window.setAlphaValue_(0.0)
			window.setContentView_(view)
			# Send the window to the front, focus NSView to execute an action action with 
			window.orderFront_(self)
			view.lockFocus()
			# Create a Bitmap representation of the focused screen frame and make it an image
			screenRep = NSBitmapImageRep.alloc().initWithFocusedViewRect_(rect)
			image.addRepresentation_(screenRep)
			view.unlockFocus()
			window.orderOut_(self)
			window.close()

			# Determine filetype, and create a representation (export to filetype)
			representation = self._getFileRepresentationType()

			# JPEG quality support (0-1)
			if format in ('jpeg', 'png', 'JPEG', 'JPG'):
				data = screenRep.representationUsingType_properties_(representation {NSImageCompressionFactor: 0.7})
			else:
				data = screenRep.representationUsingType_properties_(representation, None)

			# Write it
			data.writeToFile_atomically_("screenshot." + format, False)


----

Wow.. Python + Cocoa = ugly! Just learn Objective-C... and why is this here when ScreenShotCode exists with less hacky solutions?

----

It is the same method as the version you posted. Just a PyObjC implementation.

----

This doesn't actually work. I get a blank png...

