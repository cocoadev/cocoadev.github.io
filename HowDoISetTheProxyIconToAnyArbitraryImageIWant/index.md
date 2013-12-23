---
layout: page
title: HowDoISetTheProxyIconToAnyArbitraryImageIWant
---



Hello there, I am wondering how to set the proxy icon of a window to any that i want, i know that there is -setRepresentedFilename: in NSWindow however that has it's disadvantages as it sets the window title also, not to mention if you command click it.

So here is the question, does anyone know how to set the proxy icon to a window to any (32x32) image you want?

----

If you are using a document-based application, just change the settings for your particular document (in your target info) so that you have a document icon for your file type. --JediKnil

----

The thing is, its not a document based application, however I do need the proxy icon for better clarity about what that particular does (and it changes!), so i was wondering if there was a way to set it manually without -setRepresentedFilename: .

----

Checking the docs, I didn't even know you *could* set the proxy icon with setRepresentedFilename:. However, I think the whole point of the proxy icon is to represent a document, as it can be dragged around as if it was the document (e.g., you can drag a proxy icon into the Dock, a folder, an application, an Open/Save dialog). Why do you need one at all? And if you really do, maybe you should put your indicator somewhere else -- no one looks at the proxy icon for actual info (at least not consistently, so you shouldn't count on it) so this idea is at least slightly bad from a UI standpoint as well. --JediKnil

----

Well without going into specifics, the icon represents the application that the user is currently going to access, however we must also have a custom title.

Hmm this is interesting...

----

Using the proxy for anything other than a represented file system object breaks HIG and is therefore Bad and Wrong. Do things right, don�t be like Apple! ;-)

Having said that,     -[NSWindow windowRef] will give you a Carbon     W<nowiki/>indowRef, and you should then be able to use     S<nowiki/>etWindowProxyCreatorAndType() or     S<nowiki/>etWindowProxyIcon().

