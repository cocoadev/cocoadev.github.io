---
layout: page
title: OmniAppKitPreferences
---



I downloaded Omni's 10 public frameworks the other day and they seem really cool, but unfortunately, they're virtually undocumented and I don't even know where to start playing with them. The first thing that I'd like to do is use them to create a Preferences panel like Project Builder's, which seems to be possible with OAPreferenceController. If anyone could give me some tips on using this or other Omni classes, it would be very helpful(to others, too, I'm sure)

- DigitalCow

----

You can download a sample application, OmniPreferencesExample, that uses this at http://www.omnigroup.com/ftp/pub/software/Source/MacOSX/Applications/

-- AndrewDunning

----

*I had the same question recently. It was answered quite well on the OmniGroup mailing list macosx-dev. Here's the text of Rick Roe's response. -MichaelMcCracken *


... the OAPreferences subsystem does a few things for you:

*It give you a simple API for writing individual preference panes).
*It gives you a simple system for registering default values for your
preferences.
*It's based on OFPreference, a thread-safe wrapper for NSUserDefaults, in
case you want to write multi-threaded preference panes. :)
*It handles presentation of preference panes for you: If you have only a
few panes, you get a Mail-style prefs window; if you have a lot, you get a
System Preferences- or OmniWeb-style window. It also makes resetting
preferences to default values and providing help for each pane easy.
*If your app supports loadable bundles (plugins), it's very easy to have
them add preference panes to the app's preferences panel.


Apple has a new NSPreferencePane API that does some of this as well, but it
doesn't do everything OAPreferences does. Most notably, it doesn't provide a
pane-choose UI, so if you want to use NSPreferencePanes in your app, you
have to create your own.

*AFAIK, NSPreferencePane is for adding panes to System Preferences. I don't think it's intended to be used from inside your app?*

OmniAppKit depends upon the OmniBase and OmniFoundation frameworks, and the
OAPreferences system uses them quite a bit, so you'll need to include those
in your project as well.

**Part I:** create a preferences "client":


*Create the nib for the pane and lay out your interface. The File's Owner
of the nib should be a subclass of OAPreferenceClient which you create.
*Add an outlet in your client subclass for each UI element in the pane.
*Set the target/action of each UI object to -setValueForSender: on File's Owner.


Then, in the code for your subclass:

*Override -setValueForSender:, and write to defaults based on the value of
the UI element which invoked you.
*Override -updateUI:, and set the value of each of your UI elements based
on defaults.
*Use the 'defaults' ivar instead of talking to NSUserDefaults directly.

Of course, you don't have to follow that design pattern (and you'll probably want to extend it anyways if you have a complex UI in a given preferences pane), but it's nice and simple.


**Part II:** register your client with OAPreferenceController:

The registration system for preference panes is a generic system implemented
in OmniFoundation, called the OFBundleRegistry. You don't need to do
anything special in your code to support this, just in your bundle's
Info.plist:

First, add a dictionary at the top level of your Info.plist named
OFRegistrations. Under that, add a dictionary called OAPreferenceController.
Under that, add a dictionary with the name of your preference client
subclass. Under that, add the following keys and values:

*Required:*

*icon: the icon for the pane which appears in the pane-chooser UI.
*title: the full title of the pane.
*nib: the nib file.
*defaultsDictionary: a dictionary containing the keys and values to
register with NSUserDefaults as the default values of the preferences this
pane maintains.


*Optional:*

*identifier: (recommended) a non-localized unique name for this pane.
*category: If you app has lots of panes categorized into more than one
group, the category name this pane appears under.
*shortTitle: a short version of the title which appears in the pane
chooser UI if there's not room for the full title.
*helpURL: a URL relative to your CFBundleHelpBookFolder for the help
page for this pane.
*ordering: a number indicating the precedence of this pane when ordered
in the Mail-style pane-chooser UI. Lower numbers come first.
*defaultsArray: an array of the NSUserDefaults keys managed by this
preference pane. Use this if you're not registering default values in the
defaultsDictionary.


**Part III:** using the OAPreferences panel in your app:

In the action method for your "Preferences..." menu item, call
    [[OAPreferenceController sharedPreferenceController] showPreferencesPanel:nil];

I think that just about covers everything. Any questions? :)

--
Rick Roe
icons.cx / The Omni Group

----

*Troubleshooting info from followups: -MichaelMcCracken *

Question: why does a big black bar appear, then disappears, at the top of the window when I click on a pref icon ?

Answer: Seems that it appears when a pref pane doesn't have a valid icon.
----
*More Troubleshooting info:*

Since OAPreferenceController & OAPreferenceClient use a thread-safe layer on top of NSUserDefaults (called OFPreferenceWrapper) - it has some thread handling code in it. This code will get confused (As of the April release of the Omni Frameworks) if you don't do the following (Quoted from Gareth White):

Make sure you're linking to both OmniBase and OmniFoundation as well as
OmniAppKit. Also make sure that either 

*(a) you call [OBPostLoader processClasses] in main(), or 
*(b) your application's principal class is set
to OAApplication instead of NSApplication - this will do (a) for you
automatically.


----

I have gotten everything together, but all I get is a window with a disable reset button! 
-DannyCohen

