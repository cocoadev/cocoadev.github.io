---
layout: page
title: PreferencesLocation
---

When you save preferences, where do you put them? Which ones do you save in the user defaults? When you save them to a file, do you put it in the preferences folder in the user's library and call it com.companysite.program or do you save it in the resources folder for your program?

----

I put them in the default location, well, NSUserDefaults does. That is ~/Library/Preferences

----

Okay, that's cool. How do I find the preference file? I didn't specify a company web site or anything so how would it be saved?

----

By company website do you mean the Identifier? As specified in Targets>Info.plist Entries>Basic Information? Such as com.apple.Safari?

----

**I'd just let NSUserDefaults find them, too. That's what it's for.**

----

When I go to Targets>Info.plist Entries>Basic Information, what field do I put com.apple.Safari or what ever I want to use?

----

In the Identifier field. That's where you fill in your CFBundleIdentifier.


----

If you don't specify a bundle identifier, I believe it just uses the app name.

