---
layout: page
title: CreatingFullFledgedCalendar
---

I don't want the calendar selector, like in the lower left hand corner of iCal.  I want the big-time Calendar, where I can put appointments in and the user views them by month.  How would you suggest going about doing this?

----

Umm... set aside a few months to learn and implement the requisite Cocoa infrastructure (custom views, models and controllers).
Or you could build an AppleScript bridge between your app and iCal using NSAppleScript and save yourself the work...

----

well, it doesn't have to act exactly like the iCal calendar.  do you think it would work if I just had a grid of NSTableViews, one for each box of the calendar?  Basically, i just need to have different squares, with selectable items (maybe 4 items per square), and thats it.

*That would still require you to learn about views, etc. but would be a bad design. Do it right from the start. Learn.*

----

Perhaps an NSMatrix of custom 'calendar day' cells would work.  Might be a bit less work than rolling everything yourself.

----

I am looking for a NIB palette that shows a one month calendar view (Like iCal)

----

try ObjectLibrary

----

Okay, so I need this to work as well, so I'm taking a crack at it.  After a few hours of work, I have a pretty presentable calendar, but no place for data quite yet.  However, there is a really funky drawing thing going on, that I was wondering if anyone could help with.  Basically, when navigating from month to month with the arrows, the view will start to draw very oddly in ways that really don't seem consistent to me.  Even just hovering over the green-yellow-red buttons in the top left corner of the window will make the view change in odd ways.  However, if you then resize the window, it will fix everything up correctly and act as normal again (until changing months again).

So I was wondering if someone could take a look at my code here and see if they can see any problems or suggest anything.  Basically, it is just a subclass of NSView with all of the drawing of the calendar performed in drawRect: .  You can download a zip of my source at http://www.burgundylogan.com/media/CalendarView.zip .  Thanks for the help!  -- LoganRockmore

----

I haven't looked at your code, but in all the cases where I've seen _really_ strange drawing behavior before, the cause was unbalanced graphics context save/restores (or unbalanced lockFocus/unlockFocus, or other bits that translate down to gState stack changes).

----

Quick glance - creating your buttons, adding/removing views in the drawRect is very bad. Easiest to have them in the nib, or created programatically in the awakeFromNib...

Quick glance 2 - in drawRect you are using 'rect' the update rectangle, you probably should be using rect = [self bounds] so that it draws in the right place...

-- RbrtPntn

----

Thanks for the help.  I actually got some of it figured out, until I (quite stupidly) realized that I don't even need to have all of the NSBeizer lines in there.  I was curious, RbrtPntn, why you suggested that I shouldn't add or remove views in the drawRect.  It seems to me to be the perfect place to do so, because it will be refresed on any resize of the window or when the view originally loads.  I could put it in the awakeFromNib, but then I would have to have a delegate to check and see when the window resized, to reload the data.  Yes, it would work, but (aside from the aformentioned problems) nothing has gone wrong with adding views.  And, since I am trying to do this all with code, and no IB nibs, it made sense to me to put them there.  I was just curious why you said it's bad.  Thanks for the help, and I'll definitely post what I have when it's getting close to completion.  -- LoganRockmore

----
Quite simply drawRect should only draw things: boxes/lines/strings... Cocoa will manage most of the resizing/repositioning for you if you init your buttons/fields the correct way with an appropriate setAutoresizingMask mask. 

----
More specifically, adding/removing/moving views can cause display invalidation which can call drawRect:... a nice potential for an infinite loop. It also screws up invalidation behavior because the code does not expect to be called from the middle of drawing (i.e. validation).

Setting the autosizing mask is definitely the way to go. If it's not sufficient for you, then you can override resizeSubviewsWithOldSize: or resizeWithOldSuperviewSize: to customize how it works.

----
If you find yourself fighting/struggling against the cocoa frameworks then 99% of the time it's because you're not thinking the right way!  Sit back, read some documentation, study some (firstly Apple) examples, and try again. 

Init the calendar button/day components programatically in the awakeFromNib (or better still in IB). Then just resize/hide/show them only when the calandar date changes. You probably won't even need a drawRect method.

----

Alright guys, I've updated the zip file http://www.burgundylogan.com/media/CalendarView.zip with my most recent calendar.  It turns out, I didn't even need the beizer lines and text labels that were giving my trouble before.  I just use a different NSTableView for every date cell.  This seems to work very, very well.  Take a look at it, and let me know if anyone has any suggestions.  I plan on packing this up all nicely with correct comments and everything, and releasing it to the public, so I would love any suggestions.  I want to keep it simple, for the most part, so that people can customize it to their heart's content.

And to the previous poster:  you were right!  Well, for the most part.  I only use drawRect: to add a white background, but nothing else.

Thanks for all the help and suggestions.  -- LoganRockmore

