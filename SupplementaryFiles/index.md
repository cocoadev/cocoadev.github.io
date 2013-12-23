---
layout: page
title: SupplementaryFiles
---



**Where to put supplementary files**

Does your application use supplementary files to store data? Uli Zappe pointed out an important change of policy in the System Guidelines:

----

I want to point out that according to Apple's new standards (as described on page 163 of the current version of General/SystemOverview.pdf), supplementary files for an application belong into ~/Library/Application Support. This is a very reasonable rule.

A few days ago, I realized that practically all major Cocoa applications that are already available put their supplementary files into a folder directly in ~/Library.

This is a **very bad** thing, because as soon as you have a lot of apps, ~/Library will become a hardly manageable mess, in which other important folders will get lost among all these supplementary app folders, whereas putting these supplementary app folders into ~/Library/Application Support separates them from the rest and makes things much clearer.

It would be especially important that the already existing apps stick to Apple's guidelines, since they will be models for upcoming Cocoa apps.

I just wanted to point this out to all developers here, so that upcoming Cocoa apps get this right from the start.

----

If people are still obsessed with writing their own preferences to file (instead of using General/NSUserDefaults) then you must put the preferences in ~/Library/Preferences/.

As a personal request, if you have to make a folder in directory, then you should have placed it in ~/Library/Application Support rather than preferences.

----

If the data is not for a specific user is it okay to put it in /Library/Application Support or could there be permissions issues? Apps usually get put in /Applications which means any user can use them, so it would be silly to have the data (which is necessary for the app to be of much use) only available to one user. 

**I would suggest that data of that sort be put in the application's bundle instead.**

But what if the data might change? I'm thinking of data which is updated by the app downloading the latest data from the internet.

You can quite easily update the data in your application bundle.

Oh, I thought the application bundle is not guaranteed to be writable, I'm sure I've seen somebody say not to store things that can change in the application bundle.

**I've not. But I've not looked, either.**

**In a secure environment there is a high probablility that /Applications will not be writable.

You should put in your app bundle stuff that 'comes from the factory'. The user may be running it from a CD or a read-only disk image so don't count on being able to write to your bundle (unless you have control over what your users do, like in a lab or corporate setting). For a general shrink-wrap app or something the user will download, don't count on your bundle being writable.  Application Support is where you should dump non-document stuff like that.
