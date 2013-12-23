---
layout: page
title: WebKit
---



General/WebKit is available as an API as of Safari 1.0. Safari 1.0 installs a new version of Foundation with some URL loading gubbins. You'll need the General/WebKit SDK from http://connect.apple.com/ and, of course, your users will need to have Safari installed. I suspect Safari and General/WebKit will be included in the system in the next major release, and the headers/docs will be in the developer tools, but you never can tell.

General/WebKit builds upon two lower-level frameworks:
[Topic]

----

I figured out how to get General/WebKit to render something...

General/myWebView mainFrame] loadRequest:[[[NSURLRequest requestWithURL:[NSURL General/URLWithString:myUrlString]]];

where myWebView is an instance of General/WebView, and myUrlString is just an General/NSString containing the URL to load. That renders it, and basically, it works fine. I've also figured out some of the other delegate interfaces and stuff to control General/JavaScript and the status bar and things like that. I wrote a test app for this, I have a screenshot at http://atommic.com/images/General/WebKitTest.jpg.html

-- General/AdamAtlas

----

If you don't want to use NSURL loading (as your blog says), try these methods:

    
/*!
    @method loadData:General/MIMEType:textEncodingName:baseURL:
    @param data The data to use for the main page of the document.
    @param General/MIMEType The MIME type of the data.
    @param encodingName The encoding of the data.
    @param URL The base URL to apply to relative General/URLs within the document.
*/
- (void)loadData:(General/NSData *)data General/MIMEType:(General/NSString *)General/MIMEType
    textEncodingName:(General/NSString *)encodingName baseURL:(NSURL *)URL;

/*!
    @method loadHTMLString:baseURL:
    @param string The string to use for the main page of the document.
    @param URL The base URL to apply to relative General/URLs within the document.
*/
- (void)loadHTMLString:(General/NSString *)string baseURL:(NSURL *)URL;


But I should note that the new NSURL loading classes look very extensive. It's kinda weird there are so many; it doesn't really seem to fit into the Cocoa idea of having a small number of classes. --General/QuiSait

What is General/WebCore and what is General/WebKit? How do I inlcude to General/WebKit simply, #import <General/WebKit/General/WebKit.h> ? -- General/EnglaBenny

----

Here's the basic description of a General/WebView from the General/WebKit headers:

    
/*!
    @class General/WebView
    General/WebView manages the interaction between General/WebFrameViews and General/WebDataSources.  Modification
    of the policies and behavior of the General/WebKit is largely managed by General/WebViews and their
    delegates.
    
    Typical usage:

    General/WebView *webView;
    General/WebFrame *mainFrame;
    
    webView  = General/[[WebView alloc] initWithFrame: General/NSMakeRect (0,0,640,480)];
    mainFrame = [webView mainFrame];
    [mainFrame loadRequest:request];
    
    General/WebViews have the following delegates:  General/WebUIDelegate, General/WebResourceLoadDelegate,
    General/WebFrameLoadDelegate, and General/WebPolicyDelegate.
    
    General/WebKit depends on the General/WebView's General/WebUIDelegate for all window
    related management, including opening new windows and controlling the user interface
    elements in those windows.
    
    General/WebResourceLoadDelegate is used to monitor the progress of resources as they are
    loaded.  This delegate may be used to present users with a progress monitor.
    
    The General/WebFrameLoadDelegate receives messages when the URL in a General/WebFrame is
    changed.
    
    General/WebView's General/WebPolicyDelegate can make determinations about how
    content should be handled, based on the resource's URL and MIME type.
*/


Note that it has the following convenient IB action methods:

    
@interface General/WebView (General/WebIBActions) <General/NSUserInterfaceValidations>
- (General/IBAction)takeStringURLFrom:(id)sender;
- (General/IBAction)stopLoading:(id)sender;
- (General/IBAction)reload:(id)sender;
- (BOOL)canGoBack;
- (General/IBAction)goBack:(id)sender;
- (BOOL)canGoForward;
- (General/IBAction)goForward:(id)sender;
- (BOOL)canMakeTextLarger;
- (General/IBAction)makeTextLarger:(id)sender;
- (BOOL)canMakeTextSmaller;
- (General/IBAction)makeTextSmaller:(id)sender;
@end


There's also General/HIWebView for Carbon applications, which is simply a General/HIToolbox wrapper for General/WebView, and its only functions are <code>General/HIWebViewCreate()</code> and <code>General/HIWebViewGetNSView()</code>. You have to call <code>General/InitWebKitForCarbon()</code> first.

The headers are:


*General/CarbonUtils.h
*General/HIWebView.h
*General/WebBackForwardList.h
*General/WebDataSource.h
*General/WebDocument.h
*General/WebDownload.h
*General/WebFrame.h
*General/WebFrameLoadDelegate.h
*General/WebFrameView.h
*General/WebHistory.h
*General/WebHistoryItem.h
*General/WebKit.h
*General/WebKitErrors.h
*General/WebPolicyDelegate.h
*General/WebPreferences.h
*General/WebResourceLoadDelegate.h
*General/WebUIDelegate.h
*General/WebView.h



*Should we remove the classdump as it's now essentially useless?*

If someone would like to know more about methods or documentation for a class, it can be published here.

----

Something which I find very interesting when examining both the General/WebKit public classes and the new NSURL loading classes is that instead of listing the ivars in the main header file, they have created a *extra* class, which contains all the ivars and usually a dealloc and sometimes an init method. Examples:

    
@interface General/WebFrame : General/NSObject
{
@private
    General/WebFramePrivate *_private;
}

@interface General/WebHistory : General/NSObject {
@private
    General/WebHistoryPrivate *_historyPrivate;
}

@interface General/NSURLDownload : General/NSObject
{
    @private
    General/NSURLDownloadInternal *_internal;
}


The General/WebKit uses ?Private, while NSURL uses ?Internal. What do you think about this? I think it's useless effort and pretty stupid, as instead of accessing ivars with something as simple as _name, you would have to use _private->name. I can only hope that this doesn't start a trend.

It's actually a solution to the "Fragile Base Class Problem".  By putting the instance variables into a different memory block, you can add/remove ivars without affecting binary compatibility.  If you add an instance variable to a class, anyone that subclasses from it will need to be recompiled.  If you add an instance variable to one of these _private blocks, you don't need to do that.  ++General/MarkDalrymple

Woah...smart :). So would you recommend that people to this? Could you use a struct...or wouldn't that fix the problem? So you have, for example, <code>void *_private</code> in your header, and in the implementation file:

    
typedef struct __ClassNameIvars {
    General/NSString *_name;
    id _delegate;
    ...
} _ClassNameIvars;


and then just alloc each of the variables within the struct, but not malloc the struct itself to avoid having to use sizeof(_ClassNameIvars), which I assume would prevent compatibility. Or am I just way off?

*Way off :) Remember that this struct, being hidden behind proprietary code and a pointer, does not affect any user code. Obviously, **something** will have to malloc it, or else it won't exist! But that something is the proprietary code - the implementation file. This will need to be recompiled when more headers are added.*

*The other solution to the fragile base class problem is General/ClassClusters, which seem a much more Cocoa-ish way to do things, but who are we to question Apple?*

And...is this what's up with all the void *_reserved1; void *_reserved2 declarations in Apple's header files?

**Possibly. Another possibility is that they're padding out whether for compatibility (I think the various [and mutually contradictory] zone systems do this) or something else--maybe they're just reserving them in case they might want a couple of bytes around. I would personally never, ever want to use this sort of thing in my code; General/ClassClusters would indeed be preferable. But hey, it's not my code.**

The _reserved# things are another technique.  They're some extra space allocated for potential future use.  You can stick your ivars into the class (thereby avoiding an extra malloc, and the pointer dereferences to get to the instance variable data), and then tack on a couple of pointers to the end of your object.  If you discover you need more ivars down the road, you can then hang an allocated structure off of your _reserved1 pointer (and probably renaming it to something more meaningful), so you can be flexible and still maintain binary compatibility.  ++General/MarkDalrymple

Oh, it can also be a performance optimziation.  Like you have a class that do things in a simple way that 90% of your customers will be using (say, a scrolling list of strings), and it can do things in a complex way (a 2-D scrolling table of images).  Your object can have instance variables that are used in the common case.  When someone starts using the complex case, you can alloc a block with the extra data needed, so you only pay the price for the compex behavior when you actually use it.  (of course, it's usually better to break such stuff into multiple classes or class clusters in the first place; but I've worked on systems that used this particular pattern before) ++General/MarkDalrymple


General/HowToDoYourOwnBrowserWithWebCore
----
I have a General/NSTextView and when I click a button I want General/WebView to render the HTML code that resides in the General/TextView. How would I do that?

    General/webView mainFrame] loadHTMLString:[textView string] baseURL:nil]
----
Another [[WebKit, Webview question, I have to rendered source in a General/WebView, but I want the Title of the rendered code     <title>General/PixikonNation</title> to show as the General/NSWindow Title. How would that be done?

--General/JoshaChapmanDodson

By RTFM - [http://developer.apple.com/documentation/Cocoa/Conceptual/General/DisplayWebContent/Tasks/General/LocationChanges.html#//apple_ref/doc/uid/20002027/117641]

----
I am trying to open a new window when the user clicks a link that requires this or they choose "Open New Window" from the context menu. I followed the code from ADC, and my code looks like this:

     - (General/WebView *)webView:(General/WebView *)sender createWebViewWithRequest:(General/NSURLRequest *)request
{
    id myApp = General/[[NSDocumentController sharedDocumentController] openUntitledDocumentOfType:@"Web Page" display:YES];
    General/[myApp webView] mainFrame] loadRequest:request];
    return [myApp webView];
}
- (void)webViewShow:([[WebView *)sender
{	
    id myApp = General/[[NSDocumentController sharedDocumentController] documentForWindow:[sender window]];
    [myApp showWindows];
} 

I have a webView outlet connected to the webView in General/MyDocument.nib. When I run the program and try to open a new window, the new window's webView just loads the homepage, and the "Run" window says: 

Web Browser[3076] *** -General/[MyDocument webView]: selector not recognized
Web Browser[3076] *** -General/[MyDocument webView]: selector not recognized. Does anyone know what might be wrong?

-- General/MattBall

Nevermind. I figured it out. I was missing

     - (id)webView
{
    return webView;
} 

I knew it had to be something simple like that. 

-- General/MattBall

----

Hi, i have to display a javascript form in a webView, that works fine. But i have too to access to the data given by the user in the form.
Can I do that and how ? 

Well I have succeed to acces General/JavaScript vars with this code : 

    
_script = General/[[NSString alloc] initWithString: @"return myVar"];
_result = General/[[NSString alloc] initWithString: [webView stringByEvaluatingJavaScriptFromString: _script]];
 

It's cool, but i would have the possibility to have an action beginning when the user hit a General/JavaScript button. Any idea on how to do that ?

TIA
--Elfi


----

Why don't you just do this? Much less code, same result.. no leaks.

    
_result = [webView stringByEvaluatingJavaScriptFromString:@"return myVar"];
 

----
Right, maybe a little less easy to read. Anyway thanks for the code. 
Oh and another thing, I have found a way to trigger an action when the user hit a General/JavaScript button by using a webview delegate. 
It works only because the javascript is in a <a href></a> balises :

    
- (void)webView:(General/WebView *)sender decidePolicyForNavigationAction:(General/NSDictionary *)actionInformation 
        request:(General/NSURLRequest *)request frame:(General/WebFrame *)frame decisionListener:(id<General/WebPolicyDecisionListener>)listener
{	
	if( [actionInformation objectForKey:@"General/WebActionNavigationTypeKey"] != nil )
	{		
		if(General/[actionInformation objectForKey:@"[[WebActionOriginalURLKey"] description] isEqualToString:@"blabla:cancel"])
			General/[NSApp terminate: self];
	}
	[listener use];
}


----
I've been searching for a while trying to find the ideal HTML parser on OSX and would love to use General/WebKit. I know it is based upon kHTML but Apple hasn't exposed the parsing API's to developers. I was wondering if people have tried or are using it to parse HTML without the overkill of instantiating an entire web view? I was looking at libxml and found that General/GNUStep has wrapped it, but of course in a non intuitive General/GNUStep only way. Any suggestions on how to use General/WebKit to perform this task?

----
The parsing is accomplished by General/WebCore, which is another framework.  It is more or less an Objective-C++ bridge to kHTML.  It does create a DOM using a node-like structure, so it might do what you want.  libxml2 is part of OS X 10.3.x, so that's handy, but the Objective-C wrapper situtation is a little less than ideal.  There is some great Python sample code to do this kind of thing, but I guess it really depends on what you are tyring to accomplish and if it must be done in Cocoa.  I also don't recall how libxml2 handles deformed (as in most) HTML.

----
Do anyone have a working example of javascript and webkit to download? 

----

I put a General/WebView onto an General/NSWindow in IB, and I #include <General/WebKit/General/WebKit.h> and <General/WebKit/General/WebView.h>, but I keep getting -General/[NSKeyedUnarchiver decodeObjectForKey:]: cannot decode object of class (General/WebView). Can anyone help?

*Have you added General/WebKit.framework to your project? And use #import, not #include*

----

I'd like to make some sort of chat view using General/WebKit, similar to what General/AdiumX or Colloquy does. I have browsed the General/WebKit documentation a bit, but I don't know how to get started with this stuff. Any ideas?

----

Here's an example. It works pretty well for most cases; you just need to set up an initial document with the right structure. -- chrome (#macdev)

    
void appendTagToBody(General/WebView *view, General/NSString *tagName, General/NSString *innerHTML)
{
	// Gets a list of all <body></body> nodes.
	General/DOMNodeList *bodyNodeList = General/[view mainFrame] [[DOMDocument] getElementsByTagName:@"body"];
	
	// There should be just one in valid HTML, so get the first General/DOMElement.
	General/DOMHTMLElement *bodyNode = (General/DOMHTMLElement *) [bodyNodeList item:0];
	
	// Create a new element, with a tag name.
	General/DOMHTMLElement *newNode = (General/DOMHTMLElement *) General/[view mainFrame] [[DOMDocument] createElement:tagName];

	// Add the innerHTML for the new element.
	[newNode setInnerHTML:innerHTML];
	
	// Add the new element to the bodyNode as the last child.
	[bodyNode appendChild:newNode];
}


----

You'd do it the same way you'd do it in a web browser. If you don't already know a great deal about HTML, General/JavaScript, and the DOM, then start reading.

----

Does anyone know how to go about capturing mouseup events in a General/WebView? I would like to create an app that displays the xpath of whatever the user clicks on. There is a delegate for mouseovers, but not clicks. The mouseup method in General/WebHTMLView doesn't have any "hooks." I thought of perhaps making a class that poses as General/NSControl. The other thing I thought of is, perhaps there is some way to have javascript event handlers call me? (another user was asking about that above) There is no way to do this in the public API, and I haven't dug far enough in the code to figure it out. Has anyone already done something like this? 

----
It's fairly easy to call into your Cocoa code from a General/JavaScript handler. This is done all the time in Dashboard widgets, but the techniques work with regular General/WebView<nowiki/>s as well. I'd suggest looking up Apple's docs on Dashboard widget plugins and taking it from there.

----
I'm totally new to all of this Cocoa stuff, and am quite impressed.  I was trying to go through the tutorial on cocoadevcentral (the Build Your Own Browser one) and it said to add the Webkit framework to the project.  Now, I understand that the tutorial was written a long time ago, and I cannot find General/WebKit anywhere on my machine.  Is the new incarnation of this package simply built-in to another package now?  I found a General/WedKitSDK.pkg and installed it, but it still did not put the General/WebKit.framework in the frameworks folder.  Any help is greatly appreciated!  Sorry for posting such an ignorant question for all of you brilliant people to see.  I tried really hard to Google for what I needed, but all to no avail.
----
If you can use Safari, than it is, in fact, installed on your computer.  Are you checking in the correct Frameworks folder (/System/Library/Frameworks/Webkit.framework)?  You will find all the system frameworks in there as well. 
----
Yep, I've check the right folder, and it's not there.  And I am running a very new dual G5 with Tiger, of course, with the latest Safari.  That's what's throwing me off.  I've even Spotlighted webkit.framework and nothing comes up.
----
Something's screwy with your computer then.
----
It could be that the finder isn't showing it to you for some reason.  Try repairing permissions, and to see if the finder thinks it's hidden or something, open up the Terminal (/Utilities/Terminal.app) and type this command "file /System/Library/Frameworks/Webkit.framework", and then enter.  All that does is give you info about the given file path and it should return: "/System/Library/Frameworks/Webkit.framework: directory".  If it says something about it not existing, I suggest either re-installing OS X or trying to get direct support from the ADC.

----

Check out /Developer/Examples/General/WebKit/General/MiniBrowser/. The example of course, already has General/WebKit imported, and if you have it installed on your system, you should be able to find it by right clicking on the file and revealing in Finder. Plus you can then gleam a lot of useful information from the example project that Apple provides.

-- Cracker

----
I'm ashamed to say that I must have been automatically thinking "System/" meant my "Macintosh HD/" folder.  With opening up the minibrowser project, I opened up the Webkit framework in the Finder from there, and quickly realized that I needed to go to Macintosh HD/System/Library instead of Macintosh HD/Library.  Thanks for the simple help.  I still find it interesting that General/SpotLight does not return any results from "webkit.framework".  I almost want to delete this thread from the page, but I guess I won't mess with it.  If someone else thinks it to be worthless and feels at liberty to do so, then by all means, delete it.  Thanks!
----
Doing a spotlight search for "webkit.framework" comes up with the actual framework on my system...

*Ditto. I got four, one for each installed SDK and one in /System proper.*

AFAICT regular users can't Spotlight things in the System folder. --General/JediKnil

----
Hi there, I'm trying to develop an application based on web archives. Im able to load a page into a webview and save the content to a web archive. However, if I'm offline and try to load the web archive into my webview, none of the resources (ie. pictures) are shown. This behavior can also be seen with safari. Note, from what i can tell, the resources are cached on the system for a short period of time.

How do I force my web view to use the stored resources instead of loading them from the web? Thanks in advance for any help!

----
I'm new to General/WebKit programming and Objective-C. I'm trying to develop a plugin that has no user interface and no visible components. The only interface I want is through General/JavaScript. Here is some code:

    

#import <General/WebKit/General/WebView.h>

@interface General/PGPlugin : General/NSObject
{
}

@end



General/WebView *webView;
webView = General/[[WebView alloc] initWithFrame:General/[self window] contentView] bounds]
frameName:nil
groupName:nil];

id win = [webView windowScriptObject];

[win setValue:myview forKey:@"[[ActionMethod"];

 
I'm getting a number of compile errors. Any comments?

----

Your problem (at first glance) appears to be with Objective-C, not General/WebKit. I suggest reading up on how classes are supposed to be structured (i.e. don't put logic in the .h, don't put logic outside @implementation, etc...)
