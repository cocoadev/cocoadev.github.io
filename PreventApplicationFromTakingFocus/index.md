---
layout: page
title: PreventApplicationFromTakingFocus
---

Hi, I am building an application that I never want to take focus. I have my NSWindow setup so that when the user clicks on it it will never take focus. I also have some Dock Menu items that when clicked on will also not steal focus. However, if I click on the dock menu by itself it will always take focus. This includes launching the application and when the application is already running. I am more concerned with someone clicking it once the application is already running. Currently I have:


    
- (BOOL)applicationShouldHandleReopen:(NSApplication *)theApplication hasVisibleWindows:(BOOL)flag {
	//[NSApp orderBack:[NSApp keyWindow]];
	[NSApp preventWindowOrdering];	
	return YES;
}





I have tried many variations including trying things out in a a call back method for the NSApplicationDidUnhideNotification. Each time the application will gain focus. Is there an easy way to do this?

Hopefully some one out there will have done this, thanks

----
If your app can't be focused then it shouldn't have a Dock icon. Look into LSUIElement.


---- 

Thanks, I will look into that, but I still want the dock icon. I want the dock icon to act as a toggle switch. E.g. I click it and my app is showing, it hides, I click it and my app is not showing then it appears. However, I never want the app to take focus.

----
This is deeply nonstandard behavior and, as such, you will have a very hard time implementing it. You should re-think your interface to conform more to people's expectations and the standard way of doing things on OS X. Maybe you can get a similar effect using an NSStatusItem.

