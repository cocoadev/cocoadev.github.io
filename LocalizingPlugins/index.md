---
layout: page
title: LocalizingPlugins
---



I'm working on a dynamically loaded Cocoa plugin which loads into a variety of applications.  The plugin is localized into a handful of languages using the normal Cocoa resource localization system of .lproj directories and uses the normal CFBundleCopyLocalizedString routines to get at its localized strings.  

It all works fine if the application the plugin is loading into contains lproj folders for each language in its own bundle.  If a Cocoa app doesn't include an lproj folder for a language, apparently, Cocoa doesn't even try and do localizations for loaded plugins.

Simply going into the app's bundle and creating a (empty) lproj directory makes the localized plugin work as expected.

You'll notice that if an app doesn't include an lproj folder for a language, you don't get a localized Apple menu in that language.  Again, simply creating the folder causes the localization to take place.

Does anyone know a way around this problem?

-KenAspeslagh

----

Apple apparently wants to keep things consistent. When an app is first launched, it figures out what language it should run in by comparing the user's language settings with the app's available localizations. Once it finds the best match, the app's language is set to that language, and any languages higher up on the user's list don't count. This makes a lot of sense; an app should be consistent in the languages it displays to the user.

If you really want to make your plugin more flexible (and think hard before overriding Apple's default behavior), you'll have to give up Cocoa/CF's default resource loading scheme and make your own. You can look at the "AppleLanguages" user default to find out which language the user prefers, and load from your localized resources accordingly.

