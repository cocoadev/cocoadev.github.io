---
layout: page
title: LockedPreferences
---



Many of Apple�s preferences have a lock where one can lock the state of the preferneces pane.

To unlock it again an admin password is required.

My question is: If I were to implement a similar scheme, where would I store the state of the lock?

I mean, if I put that in the defaults, a malicious user can simply edit the preferences file.

----

You wouldn't store it. If you try it out, Apple's preference panes stay unlocked until System Preferences is quit. Then it is locked again when you open it back up. It should be implemented requiring a user to type their password EACH time they launch System Preferences.

-- Cracker

----

Actually, I think the OP is asking how to make system-wide preferences for an app, not necessarily a prefsPane. You need to store your preferences in the proper domain. See: http://developer.apple.com/documentation/CoreFoundation/Reference/CFPreferencesUtils/Reference/reference.html

As to handling authorization (requesting permission to modify the permissions in a non-user domain), see: http://developer.apple.com/documentation/Security/Conceptual/authorization_concepts/index.html

----

I do know about rights, how to obtain them etc. And I am not making a system preferences pane, just want to mimic the behavior of the lock.

The thing is, if you open e.g. Network preferences, on my machine the lock is open, i.e. I can modify the preferences. I can relaunch Network preferences or restart my machine, and the lock is still open.

If I lock it, then it is now locked, also after a relaunch or restart. And if I want to modify changes, I now need to enter the admin password.

IOW the Network preferences can be locked for changes by non-admin users, but it is not done by default.

So one would naively think that when the Network preferences are loaded, the state of the lock is read from somewhere, as the state of the lock is persistent after restarting the machine.

But where is this info stored? Clearly it can�t just be a boolean in the preferences domain, as then the non-admin user would simply be able to change it. But it also does not seem to be stored in a file changable only by root, as then a password would be required when closing the lock, unless of course OS X ships with a setuid tool to do this.

----

Well, if you're not an admin user and you try to click the 'unlock' button, it requires you to authenticate, right? The best place to store a persistent state (that is not user data) is the preferences/defaults system, right? I'd imagine this is a public (but protected by authentication) plist file somewhere, accessible through the system-wide domain. You need to authenticate to change it to "unlocked" if it's locked. All your app does is checks this flag via the defaults system and enable/disable the controls depending on the state.

When you say "Clearly it can�t just be a boolean in the preferences domain, as then the non-admin user would simply be able to change it." this indicates to me that you *do not* understand what I tried to point out earlier. There are different preferences domains. One is system-wide (authentication is required). I suggest you read up on this part of the system. You'll be glad you did.

----

What is puzzling to me, and what I tried to point out is, that if you go from unlocked to locked state, then no password is required!

So exactly what step is taken when you lock down a preferences item? Saving the state in a plist which require authentication, should then also require authentication when you lock down a preferences item!?!

