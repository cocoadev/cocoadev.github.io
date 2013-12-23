---
layout: page
title: AutoloadingAPageWithWebkit
---

I followed the following instructions to get an app to load a page automatically.

To write a Cocoa program to display a web page (in this example, it is http://www.apple.com, but it could be anything.)
1.) In XCode: Make a new Cocoa Project

2.) In Finder: Open the folder /System/Library/Frameworks and drag the WebKit.framework into the Frameworks folder icon of your Xcode project window.

3.) In XCode: Double-click on MainMenu.nib to launch it in Interface builder.

4.) in Interface Builder: In the Classes tab of MainMenu.nib right click on NSObject. Subclass it to make the class AppDelegate

5.) Right Click on AppDelegate to add an outlet named: webView of type WebView to AppDelegate.

6.) Right Click on AppDelegate to instantiate the class.

7.) Right Click on AppDelegate to create files.

8.) in Interface Builder: In the Instances tab of MainMenu.nib, * Double click on "Window" to open it.
* Drag a WebView object from "Graphic Views" tab of the Cocoa Palettes window to "Window". resize it to fill the window. In Inspector's Size tab, set the size of the Web view to the full size of the window, and turn on the inner springs both horizontal and vertical.

9.) next, Control-Drag from AppDelegate to the webView, and connect it as the webView outlet of the AppDelegate.

10.) in Interface Builder: In the Instances tab of MainMenu.nib, Control-Drag from File's Owner to AppDelegate, to set it as the delegate.
With practice, you'll be able to do the above 10 steps faster than you can read them.

Now we're ready to write some code. Add one line to AppDelegate.h to make it look like this:
    /* AppDelegate */
#import <Cocoa/Cocoa.h>
@class WebView; // <-- add this line.
@interface AppDelegate : NSObject {
IBOutlet WebView *webView_;
}
@end

and make AppDelegate.m look like this:
    
#import "AppDelegate.h"

#import <WebKit/WebView.h>
#import <WebKit/WebFrame.h>

@implementation AppDelegate
- (void)awakeFromNib {
WebFrame *mainFrame = [webView_ mainFrame];
NSURL *url = [NSURL URLWithString:@"http://www.apple.com"]; 
NSURLRequest *request = [NSURLRequest requestWithURL:url]; 
[mainFrame loadRequest:request];
}

@end


That's all. You are done. You can run it, and it will fetch the web page from the web and display it in the view.
  (FROM MIKE ASH @ http://macerudite.com/mac-xcode-app-to-display-a-webpage-38416.html)

However when I run the program it fails to show the webpage and the following error is displayed in the RUN LOG:

    
[Session started at 2007-01-02 15:02:29 -0600.]
2007-01-02 15:02:29.659 Net[2999] CFLog (0): CFMessagePort: bootstrap_register(): failed 1103 (0x44f), port = 0x3303, name = 'com.yourcompany.Net.ServiceProvider'
See /usr/include/servers/bootstrap_defs.h for the error codes.
2007-01-02 15:02:29.660 Net[2999] CFLog (99): CFMessagePortCreateLocal(): failed to name Mach port (com.yourcompany.Net.ServiceProvider)
a



I don't see any relevant bootstrap definitions other than 1103 which is service active... any ideas?

Altering the Info.plist file in the "Resources" section so that the element associated with the CFBundleIdentifier key reads something other that "com.yourcompany...", like for e.g, "uk.co.netsoft.Net.ServiceProvider" should work.

For more details, check out the Apple PDF:
http://developer.apple.com/documentation/Cocoa/Conceptual/ObjCTutorial/ObjCTutorial.pdf
in the 'Configuring Currency Converter : Essential Application Identification Properties' section.

