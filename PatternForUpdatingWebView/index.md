---
layout: page
title: PatternForUpdatingWebView
---



I'm new to cocoa (in terms of really trying to learn useful stuff) and am trying to decide if notifiers, delegates or subclasses are what I need.

I have a core data app with a url field.  I have a WebView.  I'd like to update the webview when the field is changed, or the selection in the NSTableView is changed.  I can do it in all three ways, but am looking for general pattern tips that work best with both KVC/KVO and MVC.

----

The easiest way is to (in IB) Control-Drag a connection from the field to the web view and connect it to the takeStringURLFrom: action.

----

Thanks!  That seems to work with a field but not with a NSTableView and its selections?

----

Wow, by luck I found tableViewSelectionDidChange:  wayyyyyyyy down at the bottom of:

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/Reference/Reference.html

How awful, none of the delegate stuff is linked to at the top of the document.  Ugh.

