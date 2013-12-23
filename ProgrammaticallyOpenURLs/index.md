---
layout: page
title: ProgrammaticallyOpenURLs
---

I am trying to use NSWorkspace to open a URL in Safari, but it appears that the method openURL is probably intended for fileURLs.  Is it necessary to create a file with the URL, then open the file with NSWorkspace?  I don't know any applescript and I want to be able to send info to other apps, or at the very minimum use the workspace to open various types of data.  
For example, printing sends PDF data to preview without creating a file anywhere.  How can I do this?

----

Why did you not just try running openURL on the URL you want to open? It works fine.

----
On a related note, is there a way to run items form the services menu programmatically?  I can't find any way of extracting the full spread of available services and then calling on them, especially considering that GUI-free apps don't even have a menubar to get the menu from in a hackish-way. It's just that in Safari, right-clicking on selected text brings up two items - "Search in Spotlight" and "Search with Google" - among others, and I want to duplicate that in my app.   thanks!

