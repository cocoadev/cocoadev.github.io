---
layout: page
title: CollisionWithFrameworkOutletName
---



**Beware of covering names used in the frameworks with variable names in your custom classes**

This is related to the way the framework classes use KeyValueCoding

Case In Point:'

----

I recently implemented a child window overlay to transparently fit over my display window and show some text as overlay. No big deal.

Since I'd never done this before and was unfamiliar with the API, I game my overlay window class an IBOutlet named "parentWindow" and hooked it up to the parent window instance in IB. I figured this way my overlay window could have a pointer to the parent window. I didn't realize, obviously, that NSWindow has a message already named      parentWindow  that returns the pointer for you automatically.

Anyway, even though I connected the outlet in IB, at runtime the outlet was null! I'm not an IB/Cocoa newbie -- I know I connected it correctly, so I was baffled that it should be null.

I realized quickly enough that NSWindow's parentWindow message did what I needed, and was probably causing a conflict with the outlet of the same name. But this brings up a language question... why should this be an issue in Objective-C?

My point is, in C++ you get either a warning or an error if you try to compile code where a class has a member variable with the same name as a method. I understand this, and it makes sense since they're both in the same namespace.

But why should it be an issue in objective-c? Messages are not members of a class. They're selectors; separate entities and added at startup ( or something ) to the runtime. There should be no namespacing issues. I should be able to have a field named foo, and a message named foo and there shouldn't be an issue.

Any ideas? I'm just curious here. After all, once I figured out the trouble I was able to use the built in message to do what I needed, which was to have to overlay window resize to match the parent window directly.

--ShamylZakariya

----

ObjC does not have an issue. You *can* have a message named foo and a completely unrelated ivar named foo and everything will work.

However, you run into problems with Cocoa, specifically how nibs use KeyValueCoding to hook up outlets. When setting an outlet named foo, KeyValueCoding will first look for an accessor method named     setFoo:, and only after not finding one will it fall back to directly setting your instance variable. Presumably NSWindow has a     setParentWindow: method, which was getting called by KVC.

*That makes sense, thank you*

