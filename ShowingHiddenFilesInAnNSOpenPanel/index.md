---
layout: page
title: ShowingHiddenFilesInAnNSOpenPanel
---

I want to make an NSOpenPanel show hidden files.

----

So far, it looks like it was possible under 10.2 with 
<syntaxhighlight lang="objc">NSUserDefaults standardUserDefaults] setBool:YES forKey:@"AppleShowAllFiles"]</syntaxhighlight> but not under 10.3. All the threads I've found end there. Has anyone figured this one out?

[[BBEdit shows hidden files/folders but that might be because the capability still exists in Carbon.

-SteveNicholson

----

EDIT: some people have been accessing _navView and calling setShowsHiddenFiles:YES on it. DO NOT do this. Instead, use this method on NSSavePanel (available in 10.4 and later):
[savePanel setShowsHiddenFiles:YES/NO]. See NSSavePanel.h for more information.

Thanks, 
Corbin

----

cmd-shift-period is a user feature to show/hide the hidden files state.

