---
layout: page
title: DetermineIfPrefpaneWasUpdated
---

If you double-click on a prefpane, it is installed in System Preferences. Double-clicking a newer version of the same prefpane will give you an option to replace the older prefpane with the newer one that you just double-clicked. Now, is there any way at all to determine if the prefpane was double-clicked and subsequently installed/updated? As opposed to just selected in System Preferences, that is.

I have been struggling with this for weeks now, and have found no solution, or even a single clue. I have been experimenting with most of the methods in NSPreferencePane, but no good.

 -- MarkusMagnuson

----
What is your goal, just to find out if your prefpane has been updated? There are more ways to do that than double-clicking it. The most straightforward way to catch all updates would be to write your current version into user defaults every time you're loaded, and then you can compare the stored version with the current version to see if you've been updated.

----
Actually, the goal was to check for a new version against the internet. But I just solved it all, here's some info for reference:

I got the latest version number from the internet, and then compared it to the current version of the prefpane. I got the current version by using:
    
self bundle] objectForInfoDictionaryKey:@"[[CFBundleVersion"];

If the a new version was available, the user was prompted with a dialogue to download it (or not). The problem occured when the new version was downloaded and installed (by double-clicking it) with the current prefpane still running. Because the above method would in that case report its old version string, since the bundle was already loaded. The user would incorrectly be prompted again.

So, the solution to this was simply to read the version value directly from the .plist-file, i.e.:
    
NSString *plist = [self bundle] bundlePath] stringByAppendingPathComponent:@"Contents/Info.plist"];
[[NSDictionary *theDictionary = [NSDictionary dictionaryWithContentsOfFile:plist];
NSString *currentVersion = [theDictionary valueForKey:@"CFBundleVersion"];

Since I've solved my problem, I am now removing this page from the discussions list.

----
You should restart System Preferences in this situation, because your new version won't be properly loaded until you do that. ObjC does not support unloading code, so the new version can't be loaded in place of the old without restarting the process.

----
Isn't it kind of intrusive to restart the whole application from my third-party preference pane? I am using an external helper application for the real work in my application, which is restarted on update, so it pretty much works anyway.

----
Maybe it should be optional, then. You could pop up an alert saying something like, "System Preferences must be restarted for the updated preference pane to take effect. Would you like to restart now?"

----
The hardcoded     "Contents/Info.plist" smells bad.  Did you try using     + [NSBundle bundleWithPath] in the same place you used     - [self bundle]?

----
Yes, I tried     + [NSBundle bundleWithPath] and it seems like it somehow detects that the given bundle is already loaded and uses the loaded code instead. Thanks for the tip about asking the user to restart, that might be a good idea.

----
Bundles are heavily cached. Once a bundle is loaded from a path, you'll get that same bundle again, no matter what happens on disk. In fact you can even delete the stuff at that location altogether and     bundleWithPath: will happily keep giving you the old bundle.

