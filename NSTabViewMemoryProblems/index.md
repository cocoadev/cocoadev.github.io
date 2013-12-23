---
layout: page
title: NSTabViewMemoryProblems
---

Hey everyone,

I've recently begun work on tabs for the browser that I'm working on. I have an NSTabView in my document window, tabless, and a TabViewController which is my custom NSView which handles the display of the tabs themselves. The initial tab is empty. My problem right now is that I'm getting Signals 10 and 11 on startup relating to my WebView being over-released, or so it appears. Right now, my     - (void)windowControllerDidLoadNib:(NSWindowController *) aController method is as such:

    
[super windowControllerDidLoadNib:aController];

webView = [[WebView alloc] initWithFrame:[tabView frame] frameName:@"Tab Frame" groupName:@"Group Thing"];
[webView setUIDelegate:self];
[webView setFrameLoadDelegate:self];
[webView setResourceLoadDelegate:self];
NSString *urlText;
if (queuedUrl == nil)
	urlText = [Preferences objectForKey:JBHomePage];
else
	urlText = queuedUrl;
webView mainFrame] loadRequest:[[[NSURLRequest requestWithURL:[NSURL URLWithString:urlText]]];
tabView tabViewItemAtIndex:0] setView:webView];



In addition, I have 4 toolbar items whose target is webView, which they retrieve from the document using an accessor method. No other part of the program directly accesses the webView. Right now, what's wrong is that the toolbar's trying to call the [[WebView<nowiki/>'s validateMenuItem method to determine whether these toolbar's actions (stopLoading:, reloadPage:, goBack:, goForward:) should be enabled or disabled, but the webView has been deallocated. I tried modifying my - (WebView *)webView; accessor method to return a retained copy but then the toolbar items don't validate correctly - in fact they never turn on. I don't retain, or release, the webView anywhere else. I also tried doing tabView tabViewItemAtIndex:0] setView:[webView retain but that doesn't work either.

I understand the basic fundamentals of memory management and usually don't run into troubles with them - in this case, it appears that somewhere, the functionality of either the NSToolbar or NSTabView class is performing releases that I don't approve of. Or perhaps the WebView's WebFrames are doing something as well - when I ran it though the OmniObjectMeter program I saw cases of the WebFrame class retaining and releasing the WebView.

In my test program, where I was designing the tab controller, I had a method which added a tab and inserted a new WebView using this same code (except the delegate stuff) as the subview of the new NSTabViewItem and it worked fine. I think something in the toolbar code is releasing my WebView, but I'm not sure what. Does anyone have any ideas?

----

It sounds like you definitely need to brush up on the way you're handling memory. You mentioned retaining an object ever time you return it from an accessor method-- that object is never going to be released when you're done with it, since you're not pairing it with a release message. The same goes for your code snipped up above; you're leaking memory there as well.

I have the feeling you're not going to solve your problems until you look at what's going on in a debugger. A few tips though-- you can call tabView selectedTabViewItem] view] to get the currently selected view. Also, you'll certainly want to look into the responder chain for validating and enabling your toolbar and menu items. It's the best way to handle something like what you're trying to do.

I've always subclassed [[NSTabView to draw custom tabs, rather than create another view object to act as a delegate. If your way is working for you though, stick with it. Good luck!

----

I don't usually retain objects when I return them, I'm just trying to figure out how to fix this problem. I've run it through the debugger, but the problem is that somewhere my WebView is being released that I don't have control of. All I want to do is make a WebView and stick it in a NSTabViewItem and let a toolbar control it. I've used the debugger and looked into it but as I said before, the program crashes on startup unless I retain a new copy of the webview every time it's accessed which is just an excessive way of fixing the zombie problem. All I know is that between the time the program starts and the time it crashes, it allocates one, and some code *somewhere* releases it into oblivion. Also, how would I use the responder chain to validate and enable toolbars and menu items? I don't understand how that would work, maybe I just don't understand how validation works, but I thought that the target of the action validates the toolbar item/menu item. I think I know how to use the responder chain for other actions, just not validation... Do you just set the target of the action to nil, and let it figure out what responds to that particular action, and if it responds to that action, use that responder's validate method? Also, will it always pick the front-most tab's NSResponders before other tab's NSResponders?

Also, when I was looking into subclassing NSTabView, I didn't see anything about drawing the tabs, or the structure of the view at all, so it seemed like if I were to subclass it, I'd need to rewrite the drawing code for the entire thing anyway.

----
A few suggestions:

0) Once you have made your newly allocated WebView the view of a tab view item, you presumably don't need it anymore.  The tab view item takes ownership.  Try this
    
tabView tabViewItemAtIndex:0] setView:webView];
[webView release];

You allocated it therefore you must release it.  If you are not storing webView anywhere, release or autorelease webView within the method that allocated it.  If, as I suspect, webView is an instance variable, why aren't you using a accessor to set the instance variable and thus localizing the memory management to exactly one place ?  http://www.stepwise.com/Articles/Technical/2002-06-11.01.html/ 

1) Override -release in a subclass of [[WebView, use that subclass in your tab view, set a breakpoint in your -release method, and know for sure what the call stack that releases your WebView is.  Remember that lots of framework classes may retain and release/autorelease you web view and you shouldn't be concerned.  Just  look for the surprising release/autorelease coming from your code.

2) Correct the evidently non obvious violation of the memory management rules in your code.

3) Go back to using the base class WebView.

For tool bar validation:

You seem to have a start on understanding the responder chain.  Look into NSApplication's -targetForAction: method and read about the way menu items are automatically validated.  Use a the same or similar technique with your tool bar buttons.

For custom NSTabView drawing/behavior:

Look at NSTabViewItem http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSTabViewItem.html

----

You're right about the accessor - I don't know what I was thinking, but you're right, I should be using them here. Especially because if I'm using these tabviews, I can use the tabview to get access to the webview.. it seems kind of silly to have a pointer to both the superview and the subview. I guess I've just gotten sloppy as the project has grown. I hadn't thought of overriding release, thanks! Hopefully that will do the trick - I'll report back soon.

Thanks again for the advice on the responder chain and tabview stuff. I kind of like my solution for the tabview, but I'd like to learn how to do it both ways, and hopefully I can learn how to program my menus and toolbars better with the stuff on the responder chain. I'm relatively new to Cocoa, not that new to OO, but new to memory management and strict MCV, so I've got a lot to learn.

Edit: Well the program loads now. I went through and fixed the accessor silliness, did the subclassing thing, and found out that for some reason my web view was being released by the tab view a bunch of times. I went through, played with my nib a bit, disconnected and reconnected the connections, deleted and re-added the tab view and it'll load now. I think somehow confused the tab view and tricked it into releasing my webview a bunch of times. At any rate, thanks so much for your help! Now it's time to learn the responder chain and get my toolbars and menus working :-)

Edit #2: My toolbar items are now sending their messages to the first responder (I did setTarget:nil... right?) and when the app first opens, then they validate fine. However, I click a link, and the toolbar items then all disable. If I click inside the webview, as if it were a text field and I was putting the focus ring around it, then the toolbar items validate again. Now, my first reaction is that, when I click a link, the webview loses the focus, moves lower in the responder chain, and something above it in the responder chain is catching and validating the methods incorrectly, but none of my classes implement that particular action (goBack: for example). I'm starting to think that perhaps I should put the methods inside the window's delegate, and validate them there instead of trying this fancy way of not having to write the validation code (which I know would be easy to write, but I don't know if that's the solution I want). Has any experienced this sort of dilemma where their responder seems to be losing the "focus" and not receiving actions from the responder chain?

----

That's one of the difficult things about the responder chain sometimes, when you want some responder on the window to be included even when it's not the first responder. First, take a look in the debugger and see exactly what the first responder is; it seems odd that your WebView would lose first responder status after you click a link. Like you said, you can always put your action methods and validation in your window controller or window delegate, since that will be part of the responder chain whenever your window is key. Once your application gets complex though, this can be a really bad design. However, you can also overrides -methodSignatureForSelector and -forwardInvocation in your window controller to forward these messages to the appropriate view-- that's really complex though, so worry about other things first.

----

I made an incredibly simple test program - it was document based, the document window had just a webview in it, and a button to print the responder chain, starting from the window's first responder. On launch, it looks like this:

    
2006-01-10 17:32:10.481 InterfaceTester[13725] <WebFrameView: 0x3453c0>
2006-01-10 17:32:10.481 InterfaceTester[13725] <WebView: 0x3430d0>
2006-01-10 17:32:10.481 InterfaceTester[13725] <NSView: 0x326990>
2006-01-10 17:32:10.481 InterfaceTester[13725] <NSWindow: 0x365b50>
2006-01-10 17:32:10.482 InterfaceTester[13725] <NSWindowController: 0x341290>


I then click a link. I wait for it to load, click the button again, and get:

    
2006-01-10 17:32:23.729 InterfaceTester[13725] <NSWindow: 0x365b50>
2006-01-10 17:32:23.729 InterfaceTester[13725] <NSWindowController: 0x341290>


I was able to partially solve this in the browser by sending makeFirstResponder to the window whenever the first resource is loaded in a provisional load, but the buttons sometimes turn off momentarily. I was thinking about possibly doing something with NSWindowController but I'm not really sure where to go from here. I think the problem is that the actual first responder is the WebFrameView, which is being released by WebView when the URL changes. Perhaps I should break this off into a new dicussion?

---

This looks like a bug in WebView, or at least unexpected behaviour. You should be able to fix it by adding the following code to your controller, and making that controller the UIDelegate of the webview (either programmatically or in IB). 

- (void)webView:(WebView *)sender didFinishLoadForFrame:(WebFrame *)frame
{
		[myWindow makeFirstResponder:sender];
	
}

-- Kenny

