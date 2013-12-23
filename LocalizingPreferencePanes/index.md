---
layout: page
title: LocalizingPreferencePanes
---

If you are localizing a preference pane and you want to test the preference pane in the localized language, then SystemPreferences must also have a localization for that language.

This is true at least for Mac OS X 10.3.3 through 10.3.8.

I found this out the hard way.  When I installed Panther, I chose not to install any other languages than English to save space.

Then I was sent a Japanese localization of a preference pane that I had released from a very ambitious user.  I added the localization to the project, but when I tried to test it under System Preferences, after having changed my language to Japanese, the English version would still appear.

It was only after I installed the Japanese language package off of the Panther install disk that System Preferences would show me the Japanese version of my own preference pane.

I tested this with a couple other third party preference panes that have japanese localizations, and I got the same result.

It seems that System Preferences will only load language versions of preference panes if it has a localization for that language itself.

-- a sharp old jewel

----

This is true of all localized applications loading localized bundles (including such bundles as APEs and InputManagers); the bundle inherits the application's language (which might not be the user's preferred language), ignoring any other localization in the bundle. The user's list of preferred languages is browsed iif the bundle does not have a language to match the application's one. -- l0ne aka EmanueleVulcano

