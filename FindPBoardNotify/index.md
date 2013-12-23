---
layout: page
title: FindPBoardNotify
---

I guess this has already been asked in TrackPasteboardChanges

My problem is that my app uses the find pasteboard, and I want a potentially open find-requester to update the text field when the pasteboard changes. This can either be the result of my own app placing data on the pasteboard, or another app.

So it seems I need to check the change count when my app gets re-activated and, all the places which store data on the find pasteboard, should also send out a notify.

----

Just so one can get on the same page quick, here's the current docs:
http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSPasteboard.html

I find three methods on that page that might be helpful: **pasteboard:provideDataForType:**, **pasteboardChangedOwner:**, and **declareTypes:owner:**.

Here's how I see it. If you put a new find string on the pasteboard, declare yourself its owner. If your app becomes frontmost and you want to see what's on the pasteboard, check the change count, update your innards, then declare yourself its owner again.

Now, when you **lose** ownership, as I follow the documentation, you will receive a **pasteboardChangedOwner:** notification. This can only happen when your find sheet loses focus, so no worries with LiveLock<nowiki/>ing the system repeatedly changing owners. Remember this, as you need to grab ownership again.

When you regain the focus, nobody else can *sensibly* grab ownership of the pasteboard, so it makes sense to (i) find out what's on it and (ii) replace it if it's something totally irrelevant. [If you disagree with (ii), think about it, and then post why below.] If it's not irrelevant, use it. Either way, you will also let other apps know they need to check for changes later.

When in the background, your app should be static, so don't bother checking the pasteboard over this time. If you flick the display you will freak out the user.

I cannot find a way to (meaningfully) lose a change with this system. It avoids excessive message noise (at most a handful per context shift), and does everything I would expect. (It also seems to be what you wanted to use, but in detail.)

This essay written too late for errors to be spotted. Hope it's useful :) -- KritTer

Incidentally, on second perusal...why would you want to update an already-open sheet? The other alternative is thus to not bother checking the pasteboard **at all** except when you open the find sheet. (If Apple does it differently, that's their choice, not one you strictly have to follow. Especially if the implementation cannot be perfect. If nothing else, be sure to only use it where it will be appreciated!) -- KritTer

----

You should never set yourself as owner if the change count has changed. It may be that some other have placed a dozen representations on the pasteboard that you do not know about, and taking over the ownership will loose these.

Furthermore, the pasteboardChangedOwner: method is not sent as an instant response to the pasteboard changing owner -- it will be sent sooner or later, but it cannot be used for any notify mechanism.

Lastly, Apple does update the find panel when the pasteboard changes, at least in Project Builder, and this is often a desired feature.

----
Has anybody new information on this, how we can get notifications when the pasteboard updates (� la iClip or something)?

