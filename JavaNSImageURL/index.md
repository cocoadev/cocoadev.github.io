---
layout: page
title: JavaNSImageURL
---



With java, I am attempting to load an image from a URL (.jpg) and display it in a NSImageView.  Everything appears to load properly, but my application crashes after a few seconds.  I am positive that the URL is correct.  I can even get the size of the image and it does return the correct size.  Here is some code:

    
URL nextURL = new URL("/not/the/real/url.jpg");
NSImage image = new NSImage(nextURL, true);
imageView.setImage(image);  // imageView=NSImageView


I also tried it this way...

    
URL nextURL = new URL("/not/the/real/url.jpg");
NSData nextData = new NSData(nextURL);
NSBitmapImageRep imageRep = new NSBitmapImageRep(nextData);
NSImage image = new NSImage(imageRep.size());
image.addRepresentation(imageRep);
imageView.setImage(image);  // imageView=NSImageView


Both have the same result: application loads and then crashes.  Its almost as if the image is not being saved and is garbaged collected- the next time the view tries to display, the data is not there (just a guess).

Anyone have any suggestions?
Thanks
-Ryan
----
Seems you've got some garbage collected objects somewhere in your code and as far as I can see, not related to the code above ...

----
Ok, this gets weirder.  If I add the following line:
    
image.isValid()


it always returns 'true'.  

In regards to the above post, I cannot see where anything is being garbage collected.  It does seem that that is what is happening, but I cannot figure out why that would happen if I am creating an image and then immediately adding it to the image view.  I guess that I could try adding the image to an array so that it will definitely not be removed...

Does anyone else have a method for getting a jpg from the web?

-Ryan
----
I tried to first add the image to an array list and then add the image (from the arraylist) to the webview:
    
URL nextURL = new URL("/not/the/real/url.jpg");
NSImage newImage = new NSImage(nextURL, true);
albumImage.isValid();
listOfImages.add(newImage);  // arraylist
imageView.setImage((NSImage)listOfImages.get(0));


The result that I get now is that isValid() returns true, but trying to add the image to the arraylist throws a NullPointerException.  What the heck is going on here?  I am 100% positive that the URL is correct- all I have to do is cut and paste it into my browser and I get an image returned.  

This is very frustrating.  If anyone has a technique for doing this another way, I am all ears...

-Ryan

There are a couple of problems with the above code:

* you are calling isValid on albumImage, not on newImage. I don't see anywhere that albumImage is relevant the above code block
* there's no need for the setImage function to retrieve the newly put image from the list


At the end of this code block, nextURL and newImage will be tagged for garbage collection. At some arbitrary point in the future they will be garbage collected, and thus the reference to newImage that you passed in NSImage.setImage is on an object that will disappear in the future, thus causing the crash. You will need to maintain a permanent reference to your image somewhere. It appears that your solution is to put the images into an array that is a class-level field. This will work, but your implementation has a few problems. This is one way to do it:

    
public class SomeClass {
NSMutableArray listOfImages = new NSMutableArray();

public void addSomeImage() { // this is probably an IBOutlet, but doesn't have to be
URL nextURL = new URL("/not/the/real/url.jpg");
NSImage newImage = new NSImage(nextURL, true);
listOfImages.addObject(newImage);  // I switched this to an NSMutableArray just for fun, an ArrayList would work just as well
imageView.setImage(newImage); // newImage now has a reference in the permanent lisOfImages and will not be garbage collected
}


The above code is probably not the optimal solution, as you now have an array of images in memory that will grow over time. If your image is part of a data model, then the reference should be kept in your model, and you retrieve your image from your data model to put in the NSImageView. If it's a transient object just for viewing, then your controller class that contains the NSImageView should probably have an NSImage variable (possibly your albumImage from above), that will hold the current reference to the image being displayed in the NSImageView.

-- LoganAllred

----
I tried this:
    
/* Controller */

import com.apple.cocoa.foundation.*;
import com.apple.cocoa.application.*;
import java.net.URL;

public class Controller {

    public NSImageView imageView1; /* IBOutlet */
    public NSImageView imageView2; /* IBOutlet */
	
	public void awakeFromNib()
		{
		try
		{
		NSImage image=new NSImage(new URL("http://perso.wanadoo.fr/mpergand/applis/editeur_tx7/english/tx7_editor_t.[jpg]"),true);
		imageView1.setImage(image);
		
		image=new NSImage(new URL("http://perso.wanadoo.fr/mpergand/applis/midi_dump/midi_dump_t.[jpg]"));
		imageView2.setImage(image);
		
		}
		catch(Exception ex){}
		
		System.gc();
		System.gc();
		}

}


It doesn't crash ...

Marc Pergand
----
I've run into the same problem.  I have a NSTableView with a list of DVD titles from a search of Amazon.  When the user clicks one of the titles, I go and get the cover image and display it in an NSImageView.  This works fine for the first search.  However, when the user goes to search again, I get a SIGBUS.  I've read about holding references in your java code to objects you pass into the UI and I'm doing that, but it still SIGBUS's.  Here's the pertinent code in my Controller.java class:

    
public NSImageView coverField; /* IBOutlet */
public NSTableView titlesField; /* IBOutlet */
private NSImage image =  null;
private NSMutableArray cocoaObjects = new NSMutableArray();

    public void tableViewClicked(Object sender) { /* IBAction */
		int i = titlesField.clickedRow();
		if (i == -1) 
			return;
		resetFields();
		Item item = itemArray[i];  // this is from the Apache Axis classes
		if (item.getLargeImage() != null) {
			if (item.getLargeImage().getURL() != null) {
				setStatus("Retrieving cover image...");
				setProgress(true);
				try {
					image = new NSImage(new java.net.URL(item.getLargeImage().getURL()));
					cocoaObjects.addObject(image);
					coverField.setImage(image);
				} catch (Exception e) {
					System.out.println(e);
				}
				setStatus("Finished.");
				setProgress(false);
			}
		}
	}

	private void resetFields() {
		coverField.setImage(null);
		cocoaObjects.removeAllObjects();
		coverField.displayIfNeeded();
	}


Any ideas why this is SIGBUSing?  I've looked at the stacktrace, but I can't figure out what it is doing.  Here's a bit from the crashlog.

    
Exception:  EXC_BAD_ACCESS (0x0001)
Codes:      KERN_PROTECTION_FAILURE (0x0002) at 0x00000008

Thread 0 Crashed:
0   <<00000000>> 	0x067357b0 0 + 0x67357b0
1   <<00000000>> 	0x0672a16c 0 + 0x672a16c
2   libjvm.dylib                   	0x93687ff8 JVM_GetCPMethodClassNameUTF + 0xb38
3   libjvm.dylib                   	0x936a9448 JVM_GetCPClassNameUTF + 0x998
4   libjvm.dylib                   	0x93721234 JVM_IsNaN + 0x5b24
5   libjvm.dylib                   	0x93748bc4 __floatdisf + 0xef4
6   libjvm.dylib                   	0x93776710 JVM_InvokeMethod + 0x1f0
7   <<00000000>> 	0x06870b54 0 + 0x6870b54


Lines 8 - 45 are just like line 7--just addresses.  Then there's:

    
46  libjvm.dylib                   	0x93687ff8 JVM_GetCPMethodClassNameUTF + 0xb38
47  libjvm.dylib                   	0x936a9448 JVM_GetCPClassNameUTF + 0x998
48  libjvm.dylib                   	0x937a0f00 JVM_UnloadLibrary + 0x125e0
49  libjvm.dylib                   	0x9386f2c4 jio_vsnprintf + 0x10fc4
50  libObjCJava.A.dylib            	0x89927b0c __JAVAMethodInvoke + 0xa4
51  libObjCJava.A.dylib            	0x89927d64 JAVAMethodInvokeVoid + 0x34
52  libObjCJava.A.dylib            	0x8992df30 _NSInvokeJavaMethod + 0x42c
53  libObjCJava.A.dylib            	0x8992a328 _BRIDGEMethodImp + 0x98
54  com.apple.AppKit               	0x92f276fc -[NSApplication sendAction:to:from:] + 0x6c
55  com.apple.AppKit               	0x92f2e418 -[NSControl sendAction:to:] + 0x60
56  com.apple.AppKit               	0x92f686f8 -[NSCell _sendActionFrom:] + 0x9c
57  com.apple.AppKit               	0x9301ef08 -[NSButtonCell performClick:] + 0x178
58  com.apple.AppKit               	0x92f73468 -[NSButton performKeyEquivalent:] + 0x1b8
59  com.apple.AppKit               	0x92f04e40 -[NSView performKeyEquivalent:] + 0x8c
60  com.apple.AppKit               	0x92ff8964 -[NSWindow performKeyEquivalent:] + 0x24
61  com.apple.AppKit               	0x92f517f0 -[NSTextField textDidEndEditing:] + 0x2ac
62  com.apple.Foundation           	0x90a27aec _nsnote_callback + 0xb0
63  com.apple.CoreFoundation       	0x901da4a8 __CFXNotificationPost + 0x1b4
64  com.apple.CoreFoundation       	0x901deeb8 _CFXNotificationPostNotification + 0x340
65  com.apple.Foundation           	0x90a25938 -[NSNotificationCenter postNotificationName:object:userInfo:] + 0x74
66  com.apple.AppKit               	0x92fe01fc -[NSTextView(NSPrivate) _giveUpFirstResponder:] + 0x1f0
67  com.apple.AppKit               	0x92fdfdd4 -[NSTextView doCommandBySelector:] + 0xcc
68  com.apple.AppKit               	0x92ed5268 -[NSKeyBindingManager(NSKeyBindingManager_MultiClients) interpretEventAsCommand:forClient:] + 0x668
69  com.apple.AppKit               	0x92eede9c -[NSTSMInputContext interpretKeyEvents:] + 0x448
70  com.apple.AppKit               	0x92f50e88 -[NSView interpretKeyEvents:] + 0x40
71  com.apple.AppKit               	0x92eea0e8 -[NSTextView keyDown:] + 0x2dc
72  com.apple.AppKit               	0x92eb2c90 -[NSWindow sendEvent:] + 0x1780
73  com.apple.AppKit               	0x92ea4ca0 -[NSApplication sendEvent:] + 0xebc
74  com.apple.AppKit               	0x92ead0d0 -[NSApplication run] + 0x240
75  com.apple.AppKit               	0x92f697bc NSApplicationMain + 0x1d0
76  ???                            	0x0000877c main + 0x28 (main.m:13)
77  ???                            	0x00008244 _start + 0x188 (crt.c:267)
78  dyld                           	0x8fe1a278 _dyld_start + 0x64


The really interesting thing is that if I comment out the line "image = new NSImage(new java.net.URL(item.getLargeImage().getURL()));", the program doesn't SIGBUS, but obviously doesn't display any images)!!!

Any help is greatly appreciated!!!!!
Mark

----
Hi Mark,

Not very easy to figured out where the problem is with your code, but i can say for sure that you don't need to retain your image in an array, the NSImageView retain it already.
I think the issue is somewhere else, maybe with the dataSource of your NSTableView. Do you subclass any NSControl object ?

You can look at this page:
http://perso.wanadoo.fr/mpergand/articles/en/journey_to_the_center.html
 it lists the major issues with cocoa-java.

Marc
----
Thanks for that tip Marc.  I actually have three datasources for three objects in my NIB: NSTableView and two NSComboBoxes.  I connected the datasources to three separate Java classses in Interface Builder.  I'll switch that around to use the suggestion on your web page to instantiate the classes and set the datasources in the awakefromnib method in my controller class.  I'll let you know the results! PS.  I do not subclass any NSControl objects.  I only have java.lang.Object classes.

Mark
----
I changed the NSImage creation to (holding no references in Java):
    
coverField.setImage(new NSImage(new java.net.URL(item.getLargeImage().getURL())));

Disconnected my datasources in IB and created them in awakeFromNib.  And still the same SIGBUS error on the second search.  Here's my code snipits now:

    
public DirectorsDataSource directorsDataSource = new DirectorsDataSource();
public TitlesDataSource titlesDataSource = new TitlesDataSource();
public ActorsDataSource actorsDataSource = new ActorsDataSource();

public void awakeFromNib() {
    titlesField.setDataSource(titlesDataSource);
    directorsField.setDataSource(directorsDataSource);
    actorsField.setDataSource(actorsDataSource);
}


The crash log looks exactly the same.  And if I comment out the coverField.setImage... line, everything works fine.

Any more ideas?
Mark

----

Maybe a problem with the URL, the cocoa NSURL and the java URL are toll-free bridged, but there's some problems sometimes...

try:
    
java.net.URL imageURL=new java.net.URL(item.getLargeImage().getURL());  // does it crash here ?
System.out.println(imageURL);   // is the URL valid ?
coverField.setImage(imageURL);  // or does it crash here ?


Marc
----

I'm not familiar with the term "toll-free bridged".  Could you explain?

The code:
    
image = new NSImage(new java.net.URL(item.getLargeImage().getURL()));
cocoaObjects.addObject(image);  // I've removed this since our last discussion
coverField.setImage(image);

Doesn't crash at all.  I can click on any item in my NSTableView and the image is properly displayed in the coverField (NSImageView).

It crashes when the user performs a second search.  I clear all the fields (NSTableView & NSImageView) and then perform a second Amazon search.  I've stepped through the debugger to try and determine which line of code is causing the problem, but it's deep in the Apache Axis classes (for which I'm not using source, just JAR files).  It appears to crash when Axis perform the SOAP Web Services call to Amazon.  My belief (more of a feeling that any direct knowledge) is that when the search thread is searching, some other thread is doing something (like garbage collection?) which causes the SIBGUS.

The reason I'm pointing the finger at the NSImage and/or NSImageView objects is that if I comment out the coverField.setImage(image) line, the program doesn't crash!

I changed the code to:
    
java.net.URL url = new java.net.URL(item.getLargeImage().getURL());
System.out.println(url);
image = new NSImage(url);
coverField.setImage(image);


The url prints out ok <quote>http://images.amazon.com/images/P/B000062XFM.01._SCLZZZZZZZ_.jpg</quote> for example.  Note: coverField.setImage needs an NSImage, not a url per your example.

Mark

----
*Note: coverField.setImage needs an NSImage, not a url per your example.*

Of course, you're right :)

My comments from some advices you receive from vera on the apple list:

*1) try to make the NSMutableArray static*

Don't understand why

*2) add all classes which extend NSObject to the array (don't forget the outlet
of the NSImageView)*

Unnecessary

*3) I think you are not right with your comment, that ArrayList would work just
as well, if you use ArrayList instead the CocoaBridge will destroy the
reference and the application crashes again. I don't know what the bridge is
doing with our java references but if we substitute NSMutableArray with a
ArrayList in our project the application crashes all the time.*

Well, i don't know, maybe it's safier to use NSArray and NSDictionary to store cocoa objects ...

*My belief (more of a feeling that any direct knowledge) is that when the search thread is searching, some other thread is doing something (like garbage collection?) which causes the SIBGUS.*

Maybe Axis is the issue, if you try to load the image many times with the same static url, does it crash as well ?
----
Thank you for your help.  I've gone to the extreme and put EVERYTHING into an NSMutableArray and the program still crashes!!

As an experiment, I wrote the front-end in Java Swing to see if it was a back-end (Java) or front-end (Cocoa problem).  
The Java Swing application works without a problem!
So, I think this is a Cocoa bug.  I may submit it to Apple, but as I have it working under Swing, I'm not sure how much time I want to spend with Cocoa anymore.

Mark

