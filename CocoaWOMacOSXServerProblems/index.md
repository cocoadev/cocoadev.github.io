---
layout: page
title: CocoaWOMacOSXServerProblems
---

Describe CocoaWOMacOSXServerProblems here.
Hello I'm a WebObjects developer of simple applications. The deployment
of this application is done on a MacOS X Server 10.3.2 with Java 1.4.2
and WO 5.2.2. In some of that applications I use a class that use Cocoa
API to resize user uploaded image... It works perfectly on Mac OS 10.3.2
(non server) when I develope but it doesn't works in Mac OS X Server
10.3.2 when I deploy, the instance of the application quit unexpectly. On
Mac OS X Server 10.2.8 it works perfectly... the problem appear to be
when in the Cocoa class I invoke the method:
NSApplication.sharedApplication()... Can you help me please?

Thank'you very much

    
Cocoa APIs Class
---------
import com.apple.cocoa.foundation.*;
import com.apple.cocoa.application.*;
import com.webobjects.foundation.*;

public class CocoaImageTools  {

	private static NSApplication app;
	
	public static com.webobjects.foundation.NSData
scaleImage(com.webobjects.foundation.NSData image, int maxWidth) {
		if (app==null) app = NSApplication.sharedApplication();
		float newWidth = 0;
		float newHeight = 0;
		int myPool = NSAutoreleasePool.push();
		com.apple.cocoa.foundation.NSData imageCData = new 
com.apple.cocoa.foundation.NSData(image.bytes(0, image.length()));
		NSImage originalImage = new NSImage(imageCData);
		float widthOriginalImage = originalImage.size().width();
			float heightOriginalImage = originalImage.size().height();
			
			if (widthOriginalImage > heightOriginalImage){
				newHeight = (heightOriginalImage * maxWidth)/widthOriginalImage;
				newWidth = maxWidth;
			}
			else{
				newHeight = maxWidth;
				newWidth = (widthOriginalImage * maxWidth)/heightOriginalImage;
			}
			System.out.println("Calcolo dimensioni");

			NSImage thumbImage = new NSImage(new NSSize(newWidth, newHeight));
			thumbImage.lockFocus();
			NSGraphicsContext.currentContext()
.setImageInterpolation(NSGraphicsContext.ImageInterpolationHigh);
			originalImage.drawInRect(new NSRect(0,0,newWidth, newHeight), new 
NSRect(0,0,originalImage.size().width(), originalImage.size().height()),
NSImage.CompositeSourceOver, 1.0f);
			thumbImage.unlockFocus();
			com.webobjects.foundation.NSData result;
			result = jpegImageData(thumbImage);
			NSAutoreleasePool.pop(myPool);
			return result;
	}
....
}


----

You may not be able to attach to the windowserver from the daemon if it was launched in a different context (the bootstrap context in this case, probably). I suggest you find some other Java image manipulation library that doesn't rely on a connection to the windowserver.

 -- FinlayDobbie

