---
layout: page
title: CapturingApplications
---

Is there any way to capture an application so that it will run in inside of a custom view of another? so you could maybe capture a running application or start a new one to run in there or would it require some custom frameworks?

----

(a) If you use a term like "capture", then you have to explain it. :-)

(b) No, that isn't supported.  Custom frameworks or no, you'd have to run your own mini-window server.  Even the interface to Apple's window server is private.

(c) I sure as heck hope that'd be for a developer tool, because that throws the UI guidelines right out the window.  Slightly more seriously, if you say why you want to do this, people may have helpful alternative suggestions.  

----

Because i am looking for a way to make an application that can grab another application and kind of run it in a fullscreen window but this would apply to all apps

*I don't see how that would apply to any app that uses more than one window.*

It would be sort of an kiosk software kind of thing taking an application into fullscreen

----
You might look into using a SIMBL plugin.

----
To change the window server to be used in an app?
----

Could you somehow change the window level for one app so it goes above other apps and then have a black fullscreen window behind it?

*Are you talking about kiosk mode? If not, I'm at least having fun guessing at what the hell you could possibly be talking about. :-)*

----
i am
----

*Ah. Then in that case, you're screwed. ;-) There is no method that makes this possible for *other* apps. However (and this doesn't really help you, but rather people who are searching this site for 'kiosk'), you can create a kiosk mode for your own app by reading this: * http://developer.apple.com/technotes/tn2002/tn2062.html

You should be able to make an APE or an InputManager that makes the other app run in kiosk mode. It's not going to be easy to make, though.

*No, I don't believe that's possible. The application itself would need to take advantage of the kiosk APIs. Well ... thinking better of this, I suppose you're right, but 'not easy' is a severe understatement.*

It basically seems to come down to calling SetSystemUIMode(), disabling/removing a bunch of menu items, and possibly adding a fullscreen window behind the app's windows to hide the desktop. These are all within the realm of the possible. You won't find me trying, though. :-)

Can you have the settings of one application change others real time? like SetSystemUIMode and then changing an app like that?

*No, you'd have to hack your way into the application and do it from the inside.  That's what the APE/InputManager stuff is about (InputManagers are cocoa only, by the way.  APE or mach_inject would be necessary).  Why would doing this be any better than choosing "Hide Others" from the application menu?*

Im gonna try making a SIMBL plugin that will do this. It might work although how to you get it to target all cocoa apps by making a "menu bar thing that will allow it to go into fullscreen"

**How about you tell us **exactly** what it is you're trying to accomplish instead of how you want to try to accomplish it? In other words, "I want to make an app that can ... because ..." This will allow us to suggest an alternate means if one exists because it sounds like this is a bad, bad approach that will do things to the system that you shouldn't be doing (from a user perspective).**

----
Sorry for the Confusion. "I want to make an app that will somehow select an open or not opened application and make a kiosk mode for that application. Because I have noticed that most kiosk apps are only for one app. So I want to make at least cocoa apps work, carbon if it could. And make it apply to all apps.
I have some Ideas like if the menubar was hidden you could have a "skin" where it somehow has the menu in that and customized for that specific app."

So are there any easier ways to doing this?

----

I've done something like this (bolting on kiosk-like abilities to pre-existing apps) using a SIMBL plugin.

Basically, you write your little plugin which checks for switches in the preferences. If the switches are on you do your thing, this way your code can be turned off if stability or other problems arise. The "controlling app", should you implement it, just flips whatever switches your plugin checks for.

I was only targeting a specific version of a specific Cocoa application, for personal use at that. This way I could make some assumptions that wouldn't be ok otherwise. I could rely on class foo being there to provide me with xyz, etc. Doing it on a system-wide scale would be more difficult...there's no     -goIntoKioskMode method. If this is just a learning exercise I'd say forget it and set your sites lower. Get your code into another app first and dink around.

As far as "capturing applications" goes, I think you've found your answer; SIMBL(/InputManager), APE or mach_inject.
----
Some of the work could be potentially done with poseAsClass: using a custom window class.
----
Most of the work for something like this would be in doing something different for each app. Forcing TextEdit into fullscreen is different than doing the same for iChat. That's what makes it "impossible" - how do you man-handle new apps or ones you haven't specifically accounted for?

The only way I can see to make a "kiosk" out of *any* app is if you force the frontmost app to stay frontmost no matter what. Then you don't have to figure out how to make each app fullscreen-able. You probably don't need to inject your code into other processes. You could watch for a keycombo to bring up the getmeoutofthekiosk dialog..etc.

Something like that would be way more do-able, IMO.

----

You could disallow app switching, bring all the app's windows forward, and hide everything else (either by hiding the apps or by putting up a fullscreen colour/image in front of them).

As to how to do any of that, you've got me (:

