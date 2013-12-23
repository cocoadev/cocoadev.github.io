---
layout: page
title: CustomizingWebViewUsingCarbon
---

My goal is to customize the contextual menus in WebView within a Carbon application. I'd like to know upfront if this is even possible. HIWebViewGetWebView can be used to access WebView methods like     -reload and     -goback, but what about     -contextMenuItemsForElement?

I need to use Carbon because I'm trying to accomplish this with REALbasic, which currently makes its apps in Carbon (though I believe this will change soon). It already has a control that accessed WebView, but it isn't able to customize contextual menus, which means I'll probably have to somehow make a plugin that uses contextMenuItemsForElement.

All help here would be very appreciated.

(I orphaned this a while ago to try something else but I'd like to put it up for discussion again.)

----

I don't believe this is possible.  You'll likely have to use Cocoa for this, since WebView itself is a Cocoa class.

----

It's possible to mix Carbon and Cocoa in the same project. Why not do so?

