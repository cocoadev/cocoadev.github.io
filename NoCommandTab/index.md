---
layout: page
title: NoCommandTab
---



How can a running cocoa app remove itself from the Command-tab list?

My applicataion is a background app that almost never has any windows to display unless the user needs to configure something.  But I don't want it to run entirely in the background since I want a Dock icon to show up that has some options and status on it.

How can I make the app remove itself the list of apps that Cocoa changes between using Command-Tab?  I know it's possible since there are apps (e.g. dashboard) that are running, but are not in the Command-Tab list.

I want to be able to turn on and off my app's presence in the Command-Tab list based upon whether my app has any open windows (but normally it will not have open windows)

Thanks -- JoelSmith

See LSUIElement

---- Dashboard is actually... like too many other things... handled by the dock itself, which is why it has special status in regards to that pseudo app. Short story, if you must have a configure thing, either have a sseperate app, or a system preference. Unless of course someone else can come up with a better solution.

---- Thanks for the link to LSUIElement.  I wanted to do that originally, but I didn't know there was a difference between NSStatusItem and NSMenuExtra, and the HIG says that only Apple should make Menu Extras, so I thought that the icon on the menu bar wasn't allowed.

I was just hoping there was a trick to do it after seeing the DontExposeMe article. -- JoelSmith

