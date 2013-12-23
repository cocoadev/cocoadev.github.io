---
layout: page
title: SmartPlaylist
---

I would like to create an iTunes like Smart Playlist.  I have two controllers.  One for the source table view and another for the content on the right.  The interface looks exactly like iTunes.  I would like to create a smart playlist right from the search field.  How can I create a "smart playlist" so that when the user selects the "smart playlist", it would create a live search on the library's contents.  I also want it to automatically update it's contents if there is another entry added to the library.  Thanks for the help, if any.  Thanks.

*That is such an astoundingly broad question. It's unlikely that anyone could answer something like that with so little information.*

Probably the best idea in this instance is to wait for Spotlight. :)

----

Yep. Basically, the answer for how to create a smart playlist like in iTunes is "query your database and display the results."

----

Great! I successfully did the "query your database and display the results" option.  Works great!  I even have it done by selecting what to search for on the fly!  But I also want to apply a "iTunes" smart album edit panel.  Where you press the plus button and you add another filter to the group.  How can I do that?

*Read this tutorial:* http://www.stepwise.com/Articles/Technical/2003-12-20.01.html

----

Yeah, I read that already.  But the interface is not classy at all.  Anyone know how apple does it?

*Well since I'm sure a lot of us work for Apple we could tell you...*

----

Apple does it in Carbon.

Anyway, if the interface isn't classy enough for you, design a new one.

iPhoto isn't Carbon.

Neither is Mail.

----

The app being talked about is iTunes, which is in Carbon. But the point is, it doesn't matter whether it's Carbon or Cocoa. It can be done.

----

Yes, the question is.. how can it be done? ;)

----

Read this tutorial: http://www.stepwise.com/Articles/Technical/2003-12-20.01.html

if the interface isn't classy enough for you, design a new one.

echo... echo... echo...

----

Check out the MailSorter.nib in Mail.

----

You can try this  (xcode project in progress): http://perso.netpratique.fr/escoz/CriteriaView.dmg

No readme, uses bindings, takes parameters from a plist file in the Resources folder.

