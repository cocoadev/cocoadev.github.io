---
layout: page
title: HowToCreateASystemPreferencesPane
---

I would like to create an SystemPreferences pane for my daemon. I don't know where to start : Do I need to create a bundle or plugIn (or something else)?

----
See the tutorials at [http://cocoadevcentral.com/articles/000030.php] and [http://cocoadevcentral.com/articles/000040.php]. Happy programming!

Apple has a tutorial of sorts: [http://developer.apple.com/documentation/UserExperience/Conceptual/PreferencePanes/Tasks/Creation.html]?

I just discovered something peculiar about localizing Preference Panes.  See LocalizingPreferencePanes.

----

I want to make an app that will be a sort of proxy for the SystemPreferences application, except it will save the last prefPane opened and default to it the next time SystemPreferences opens.  I originally thought of doing this with APE, hacking system preferences for a WillOpen and WillClose delegate, but realize it would be simpler and safer to just open the prefPane file directly. I still need a way to find out which pane was selected when SystemPreferences is closed.  Is there a way to get that information from the application (perhaps a     -(NSString)getCurrentPaneSelected method), and a way to stick a delegate to the WindowWillClose message (if this is the correct message to delegate..)?

----

To what object would you propose sending this supposed     getCurrentPaneSelected method? SystemPreferences wasn't designed with this in mind. If you want to get at SystemPreferences' internal data, you're going to have to hack the app.

*SystemPreferences deals with a lot of sensitive data, so I imagine it was designed to be as hack-proof as possible. I doubt this project is a worthwhile way to spend one's time...*

