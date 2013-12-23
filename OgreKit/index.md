---
layout: page
title: OgreKit
---



http://web.me.com/oasis/ogrekit/

Regex framework, released under a BSD license. Supports multiple regex syntax styles and is a drop-in replacement for the standard Cocoa text-finding stuff. The only documentation for it so far is in Japanese, so perhaps this page could be useful for those of us who don't read Japanese to share information. This is AFAIK the only Cocoa regex package that provides user interface elements such as a find panel and a 'find all' results panel.

Notably used in SubEthaEdit. Also in the open-source editor TextForge. Not necessarily in that chronological order.

Can you use this in your shareware app LEGALLY?

Yes. A copy of the offical BSD licence is available at http://www.opensource.org/licenses/bsd-license.php. Yes, you can use BSD licenced code in your commercial software. -- GCM

I see.  Now according to that license I would have to place the copyright info somewhere within the application? - or am I completely wrong about how this should work :)

Take a look at this sample chapter about the BSD licence at http://www.oreilly.com/catalog/osfreesoft/chapter/ . You should really include a credit to the BSD software in your credits regardless of whether you have to. Also consider sending the authors an e-mail to let them know how their work is being used. -- GCM

*I'm sure a free license for your app would be appreciated too*

----

Does anyone know if this framework can determine which one, out of several expressions, matches best against a given string?

*I'm not sure any piece of software is going to be able to do that. What do you mean by 'best'?*

I want to test any given string against a list of expressions; say, 'www.example.com' against:

(.+\.)example.com 
or 
(www\.)example.com

(Simple example) They'll both match, but the second one is more specific. I'm wondering if there's a way to know which one matched more precisely, or even to know which part of the expression matched (the expression part, not the matched string).
----
Yes, that's possible. Do e.g.:     ((www)|(.*?))\.example.com. Alternations work left-to-right, so it'll prefer     www over     .*? and it will furthermore make it available in capture register 2, if matched, otherwise the     .*? match will be in capture register 3.

----

Some english docs would be nice for this framework.
----

Thank God he speaks english !
----

The google translation of the japanese documentation is pretty good not that I can speak japanese to confirm it, but it seems to make sense
----

This can also help:

http://www.geocities.jp/kosako3/oniguruma/doc/API.txt

----

BEWARE: that Ogrekit is NOT thread-safe.

