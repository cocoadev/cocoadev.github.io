---
layout: page
title: PuttingAutosaveSettingsIntoSpecificSuite
---



I've written an add-on tool that can be run within several applications. This tools contains a NIB that has windows, NSBrowsers, NSTableViews, etc. and each have an autosave name so their settings will be retained between runs of the application/tool. However, these settings are saved in the running application's preferences, so each application will have their own set of settings for the tool's UI elements, and I think it would be more desirable if the tool's settings were maintained across all applications.

So I'm wondering if it's possible to have these autosave settings be sent directly to a specific preferences suite, and what I might use to accomplish that (CFPreferences? NSUserDefaults? Other?) The Reference Library hasn't really provided any insights if this is possible.

