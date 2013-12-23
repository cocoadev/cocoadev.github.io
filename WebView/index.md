---
layout: page
title: WebView
---

A General/WebView is part of General/WebKit, similar to a browser view in Safari - allows an app to display a web page (obviously). You can even allow the General/WebView to edit HTML pages by setting its editable flag to YES. For a quick tutorial, check out: http://www.cocoadevcentral.com/articles/000077.php

----

For retrieving the HTML content of a web view, see General/GettingHTMLSourceInWebView. http://goo.gl/General/OeSCu

----

I'm having some problems with General/WebView in edit mode. It's just acting messed up and buggy in general.  I read that Apple uses an editable General/WebView for Mail.app (probably to get the pretty quoting effects easily). The editor acts perfectly fine in Mail. The problems I'm having are below.

**Lists**

Bulleted lists are a joy ... If the source is as follows:

    
<ul>
    <li>One</li>
    <li>Two</li>
    <li>Three</li>
</ul>


----

I want to subclass General/NSScroller and use it in a General/WebView, so I can change the look of the scrollbars (at very least the size). I know General/WebViews? usually have lots of frames, but I'm injecting the HTML, so I have control over that. I can get the General/NSScroller and General/NSScrollView objects just fine, but I can't figure out what to to from there. "- (void)setVerticalScroller:(General/NSScroller *)aScroller" doesn't seem to work. I've subclassed with Interface Builder before just fine, but I can't seem to understand how to do it programmably. This is about all I could find: http://lists.apple.com/archives/webkitsdk-dev/2003/Dec/msg00004.html.
 ... and you place the cursor before the 'T' in Two and press return, a blank line is created. So far, so good. However, try clicking onto that blank line and inserting text. Not possible. If you type something before the T, press return to separate it, place your cursor before the newly-inserted text and press delete, all is well again. Strange behavior.

**Horrible Markup**

General/DOMCore *outright mangles* your markup. All the whitespace is screwed up (even on items you haven't edited) and the tags are all upper-case. (TBODY comment removed from previous post because I stupidly did not realize it's part of HTML4.0 ... duh).

**Unexpected Loss Of Data**

I haven't been able to reliably reproduce this, but occasionally, when you select text and type to replace it, the **entire content** of the document is replaced.

Can anybody offer any insight on taming this (potentially powerful) beast?

**Update** - According to an Apple developer, the list and table editing issues are problems they're working to resolve. The data loss issue has been heard of but they need bug reports. If you've experienced this, please provide information to Apple via General/RadarWeb.

----

I'm having difficulty catching spaces in my General/WebView subclass.  keyDown: simply does not respond to space for some reason.  Surprisingly enough, when a window contains a webview, it also does not catch space.  It catches everything else just fine (such as 'm','c',etc.).

Secondly, I'd like to form some sort of bridge between the javascript in the view and the program.  For example if something is clicked in the webview I'd like the cocoa code to notice, if that makes sense.  Does anyone know how to go about doing this without getting down and dirty with General/JavaScriptCore?

----

For your first question, this is probably because the General/WebView hierarchy already catches spaces. (If you press space, it scrolls down the page.) It doesn't catch the others. If you're overriding keyDown:, then the first responder is eating the space events before you can handle them. If you really need to handle spaces differently, you'll probably have to override sendEvent: in your window.

About the second question, I'm not sure of the details, but I believe the "standard" way of doing this is to have the General/JavaScript program try to display an alert with a specially formatted string. One of your General/WebView delegates can catch the request, parse the string, extract the data, and act accordingly. I'm not sure if this allows data to be passed back. This has been discussed on Apple's General/WebKit mailing list, so I suggest looking there if you need details.

----

I continued my tests with General/WebView, and this I think pretty much exemplifies the problem.  If I have a normal General/NSView subclass and write keyDown, after making the subclass first responder, space bar events are caught.  However, interestingly enough, as soon as I insert a General/WebView as a subview of this General/NSView, it stops catching spaces.  Why is this?

----

Because the General/WebView (or a subview of it) is becoming first responder, not your General/NSView subclass.

----

That explains something I've been wondering about for a while. You know how you can have QT media open in your Safari (or other browser) window? Quicktime shortcuts (like space for play and pause) control the player as usual, but Safari key actions (like using delete to go back a page) do not work at all. Evidently (because of the first responder change), this applies to any Cocoa app with subviews...anywhere!

----

The responder chain seems to be simultaneously one of the most important things in a Cocoa app, and one of the things that Cocoa programmers know least about. If you aren't already intimately familiar with how it works, do yourself a favor and read all of Apple's conceptual documentation surrounding General/NSResponder.

----

I have a window (made with IB) that has two webviews (also made with IB) in it. I capture the entire screen and then create a borderless window. I then retain the webview I wish to reuse in the exact same window as it is made in later on. Then I set [myFullscreenWindow setContentView:myWebView]; It works, and I can see all my data fullscreen. However, when I release myFullscreenWindow and look at the IB-implemented window, the webview is missing. I'm not quite sure how to proceed so that I have the same window as I started with.
----
Did you get this, the missing webview solved? I got the same issue. Thanks

----

If you are looking to use setAllowsScrolling: to enable/disable the scroll bars, it doesn't work quite well. Instead, use Javascript to solve the problem.

    
bool val = NO; // this value is to enable/disable scrollbars
General/webView windowScriptObject]evaluateWebScript:[[[NSString stringWithFormat:@"document.body.style.overflow='%@';",val?@"visible":@"hidden"]];

