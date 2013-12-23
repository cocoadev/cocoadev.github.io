---
layout: page
title: WebViewLinkToOpenInBrowser
---

I have a General/WebView which loads a web address, when the user clicks on a link I wish the link to open a page in Safari (or whatever browser is  the default). How would I achieve this?

----

Using General/WebKit's built-in methods for handling a link click, you can call General/NSWorkspace's built-in method for opening a URL (which opens in the user's preferred browser, rather than rudely forcing them to use *your* choice instead).
