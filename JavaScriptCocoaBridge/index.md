---
layout: page
title: JavaScriptCocoaBridge
---



As of version 10.4 "Tiger", MacOS X includes a JavaScript Cocoa bridge which allows Dashboard Widgets to communicate with Objective-C objects.

----

The bridge is available as part of WebKit, but cannot be used separately from WebKit classes (as the JavaScriptCore framework that handles such things is private); you have to create a WebView and program things within the HTML page using the API to evaluate JavaScript<nowiki/>s. See the docs for the WebScripting protocol and Dashboard plugin creation for more info.

