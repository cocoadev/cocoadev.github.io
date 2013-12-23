---
layout: page
title: SpiderMonkey
---

The JavaScript implementation used in Mozilla is called SpiderMonkey. It is also available as a seperate library and can be embedded in own applications:
  http://www.mozilla.org/js/spidermonkey/

OpenGroupware.org/SOPE includes NGJavaScript which is a SpiderMonkey/Objective-C bridge:
  http://www.opengroupware.org/cvsweb/cvsweb.cgi/OpenGroupware.org/SOPE/skyrix-sope/NGJavaScript/
  
----

Anyone know how this compares with e.g. JavaScriptCore?

----

 The JavaScript implementation used in Mozilla is (far ?) better than the JavaScriptCore. In my last application I have to used Webkit and his JavaScript implementation and on some complex pages with lot of JavaScript the JavaScriptCore crashes whereas Mozilla just run fine.

----

SpiderMonkey is the JavaScript reference implementation.

----

I want to embed the Mozilla JavaScript Engine in my application so that i can use it (JS Engine) as a plugin system...
has anybody done this before ???

----

Which one? Rhino or SpiderMonkey?  Rhino is easy to integrate into a Cocoa/Java application (as the interpreter is in Java)... I have no experience with SpiderMonkey (the C/C++ implementation), but if you are just arbitrarily picking a JavaScript engine, have you considered WebKit's support?

----

I intended to embed SpiderMonkey. but what are the advatages of Rhino in in there relation to Cocoa????  - SimonAndreasMenke

----

I've embedded JS SpiderMonkey in my app using : http://www.opengroupware.org/cvsweb/cvsweb.cgi/OpenGroupware.org/SOPE/skyrix-sope/NGJavaScript/

really cool stuff...

see also topics like WebKit and JavaScriptCore

