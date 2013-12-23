---
layout: page
title: AutomatorLikeUI
---



Hey, i would be very interested by creating an Automator (cf. Tiger preview) like interface, which means:
The ability to create dynamicaly "grey blocs" in a list.
http://images.apple.com/macosx/tiger/images/automatorretouch_20050113.jpg
Those blocs can be closed/moved... and contains several UI objects like fields and buttons.
I'm searching for tips to release such an interface.
Do i have to instanciate all objects dynamicaly?
Or can i make a template of a bloc in IB and insert the Nib file into my main window?? (i don't think it is possible to do this but i'm just starting in XCode).
I managed to create buttons and fields dynamically already but it's quite a work, so any tips?- StephaneDassieu
----
Well regarding the grey block, they shouldn't be too hard to make.  I would make a NSGreyBlockView that just drew the grey outline with the title or whatever, and contained a content view that you could set up in IB, or access dynamically: myGreyBlock contentView] addSubview: myTextField];  After that just make a custom [[NSView to hold the grey blocks and the only hard part will be having the blocks "snap into place" when draggin them.- FranciscoTolmasky
----
I was thinking about writting all the UI dynamically.. but as i said it's a lot of work (i'm not lazy, just trying to save time).
When you say "�contained a content view that you could set up in IB�", you mean i can make an UI in a window with IB and then import this window/nib content/view into my own "NSGreyBlockView"?- StephaneDassieu
----
i had a rudimentary setup which did Some of the work you are describing.  Its been a few years but I think you have two options here.
1. make a table view with an expandable cell, and then follow the "view in table trick" found at stepwise.com to get your custom view to be a child of it. - i am not a fan of this method. it feels limited, and klugey. of course its going to draw pretty fast, but if thats the only benefit, I'm not buyin.

2. make a custom view class that organizes its own subviews, which are of a custom class, and can collapse, and contain subviews themselves.  Then in IB, you make a view, populate it with your stuff (give it controllers and the like), and then convert the envelopingView into your custom subview.  you obviously have to have a way to add them to your parentView (which you have instanced into your main window), lets assume that you are using drag n drop... you can find the nib file and its elements by asking the app for its bundle, and then create an instance of the whole pile of views and then "give" them to the drag n drop mechanism. 

the main idea here is that you CAN make a widget in IB with any complexity, and as long as it is internally and externally consistent, you can treat it as one unit and load it both programatically, and through the IB interface. What you want to do is make one that you can re-use, and populate in IB, and then add without much work in XCode.  Its doable.

my setup loaded my subviews from various bundles, and had to generate a list at app startup of what was available. I dabbled in making IB palettes, working with bundles (both for the instanced subviews, and for the prototype subview class, which I linked to from my subview types), and I developed a consistent and transparent way for my subview classes to tell some object what is happening inside of them.  I setup a default controller object for the subview (tied to it in IB so that it goes along for the ride) which kept a dictionary, which can hold any amount of values. then whenever my subview's children were altered, they told the controller, which adjusted its value in the dictionary, and then told its controller object that its dictionary had changed. That way, I only ever had to write one controller, and its language was simple, and expandable, because I intended to build on the UI.  fun stuff.

I would suggest the topics of:
bundles, how to load nibs, and how to work with them in the filesystem
IB palettes
Views (especially how they work with contained views, how to alter their size, and coordinate systems... an inverted y could SAVE you months of work)
Tables, and matrices, as you will no doubt be duplicating some of the work they do.
and get used to the idea that the UI elements that you see everyday are examples of what you are trying to accomplish.  it is not only possible but done every day.
----
Wow, i obviously still need to learn XCode and Cocoa, but the help you just gave me is really motivating. Thanks a lot for those tips. I already started the CustomView/SubViews thing, but i really wasn't sure that it was possible to convert and load nib files.. It's great, thx! - StephaneDassieu
----
Despite my hard work and researches, i haven't been able to dynamicaly "draw" a nib file in a view. Can i have a piece of code for it? As i wrote in LoadNibInNSView discussion, my attemps are really poor. - StephaneDassieu

----
You probably want to make a nib with a view in it and load the view. You can't "draw" a nib anywhere, you just load objects from it. A friend of mine and I wrote a class that does something like this based on Adium's AICustomTabsView class. It was pretty unfinished though; the intention was to make a Finder-like sidebar with reorderable/draggable/collapsable items. - DavidSmith

----
I've put an example here: http://homepage.mac.com/ryanstevens/temp/NibLoading.zip

----
Damn! I didn't knew it was possible to directly instanciate a NSView subclass in IB! How dumb i am! It's so obvious now! Thanks a million time for this example, really simple and neat :) As i'm really noob in cocoa and XCode i'm trying not to bother the community with too many questions but it's sometime pretty hard to be on your own and i haven't ever seen any examples of such things. Thanks again for your time. - StephaneDassieu

*No problem, just glad I could help clear things up. :-)*

*I think I'd have to use it first so when I get my hands on Tiger I may try tackling this if it hasn't been done. Here's to hoping it comes out (and I can afford it) soon.*

----

Please can someone place the sample back, thanks

*Done.*

*Related subject : ExchangeDataBeetweenClasses*

----

*"...make a custom view class that organizes its own subviews, which are of a custom class, and can collapse, and contain subviews themselves..."*

This is precisely what I am trying to tackle right now, to do something very like the Automater UI.  Instanciating custom views and populating them from a NIB file is no problem, but I'm having a devil of a time sorting out the 'proper' way to do the custom view class which organizes its subviews in a properly efficient manner.  Any pointers? --RachelBlackman

