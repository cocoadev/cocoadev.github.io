---
layout: page
title: StandardService
---



A StandardService is one of those things you find under the Services submenu of the application menu in Cocoa applications.

A StandardService is basically AnApplication that runs in the background, and instead of ending with the .app extension, it ends with .service, and usually won't have any interface to speak of.

The Info.plist needs to specify LSBackgroundOnly = 1, in order to assure the service does not appear in the dock.

Services are used to provide some functionality from your application throughout the system: Grab provides a StandardService for taking screenshots, and Mail has services for mailing to someone, and mailing the selected text.

When developing Services, be sure to remember that at the time of this writing (February 2002), few Carbon applications take advantage of services (an example of one the does is BBEdit).

Some more background info can be found here (It's all still from the OpenStep days, but should pretty much apply to MacOSX, as well):

http://www.stepwise.com/Articles/Technical/Services.html

----

Unfortunately Apple seems not to be paying a lot of attention to Services and as a result they are not as polished as other features. You should be aware of some bugs and problems in their current implementation (and write to your Congressman to have them fixed)


* Services can be run from the menu bar. You will have to navigate through several sub-menus to find the Service you are looking for. NeXT-Step had one level of menu hierarchy less by making the Services menu a top-level menu (like Help is now).
    *  Services can be run by typing keyboard shortcuts, which is much faster than having to use the mouse. However, many applications these days already define shortcurts that use up the whole keyboard, taking the shortcuts away from the Services. You have to check in every application if your Service shortcut is available. If not, you will be executing some application-specific function instead. You want to be careful here.
    * Even if the shortcut is available, you cannot use them before touching the application menu bar  first with the mouse pointer. That is a very strange and old and still open bug in Mac OS X. Only after the menu bar has been used for the first time can you use the shortcuts for Services.
    * Ideally, Services should be available in all text areas in all Cocoa and Carbon applications (I am not sure about support in Classic and Java applications). Unfortunately, that is not always the case. With some programs, you are just out-of-luck. Here are my experiences with web browsers: If you are using Safari, you can use Services, but you cannot use Undo. If you are using Camino, you can use Services only in some versions. If you are using Mozilla or Firebird, you cannot use Services.



----

See MakingServices and MakingAppServices for some sample code.

