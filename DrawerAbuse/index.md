---
layout: page
title: DrawerAbuse
---

<sputter> Dear God No!!! Drawer Abuse Stops Here! I am utterly tired of seeing shareware apps w/ scads of controls all buried in layers of control drawers! For example, check out Starfish 2.0 on VersionTracker. If this isn't the definition of Bad UI, then i don't know what is!

For that matter -- look at how Apple is using Drawers! Look at Mail. Are auxilliary controls hidden there? No, the drawer holds the mail list, which is essentially the main window's (Mailbox Viewer's) parent interface (Mailbox List). 

Look at iChat's use of Drawers: identifying the List of people in the current chat; defining groups (lists!) of people. I suppose you could claim a list of people is an auxilliary control, but that's wrong. The important thing is these are *lists* that the user is meant to drag things into/outof -- hey, kinda like Mail.

Look at Safari's use of Drawers. Safari has lots of auxilliary controls, right? So let's see ... hmm ... well now, I don't think Safari uses Drawers at all! It uses a variety of interesting ways to store auxilliary controls, including using items in the main menu.

Sure, Xcode buries GDB in a drawer. But Xcode seems to be moving away from that decision, since they now allow GDB to run in its own window. Click on a project's Build Styles tab and you'll get a drawer -- this drawer is again similar to the Mail / iChat interface: a list of categories you can click on to refocus the main viewer.

The next time you consider using a Drawer, stop and think about it. If you're just trying to create more space for your clutter, like that junk drawer in the kitchen, find a different way to organize your UI. If they're just controls, use a tab view or a button bar. Or use smaller controls, menu items, customized widgets, etc. Use drawers for what they're intended for -- a way to organize lists of data that's directly related to the main content viewer.

-- MikeTrent

----

Thanks for the feedback, Mike. I cleaned out the suggestion about using drawers in the other discussion.

This forces me reconsider the way I am designing a couple of my own UIs.

One of them is a visual app (not document-based) that draws in the entirety of the window's content area. The (somewhat mathematical) drawing routine requires a healthy set of parameters entered from text fields, which I have tucked away in a (now severely-deprecated, but solitary) drawer.
Fortunately for the shareware community, I have not inflicted this on the public as yet.

It is certainly true that a tabbed view with one tab for the controls will take care of this in an exactly equivalent (no, a superior) manner.
But a toolbar with a dozen text fields would be kind of an abomination.

Don't know why I got fixated on drawers for controls. At the beginning, it was just enough to get the damn thing working.

But please stop sputtering. That is so unlike the Cocoa equivalent of a Zen master that you normally appear to be.

*Zen master? -- MikeTrent *

----

While I generally agree that drawers are **way** overused, I think having controls in drawers is acceptable for non-essential controls in one-and-only-one window apps (like Starfish - I agree its UI could be better, but I don't think it's due to the drawers.) If having controls in drawers is the alternative to having a separate inspector window to contain them, I'll take the drawer.

*Starfish used to have a most-excellent tabbed UI. Things were organized cleanly, even if you couldn't see everything at once. The new setup, you can see everything at once, if you open the gull-wing drawers. If I position the window against one side or another of my screen, I can't see the drawers; that's a problem. Also, the drawers (IIRC) were visible only by selecting a special menu item (or control? haven't looked in a while) so they were less discoverable than a tabbed interface. So, I think Starfish 2.0's problem is entirely because of its drawers. -- MikeTrent*

Another solution to DrawerMadness is to have a disclosure triangle to hide/show the controls, just like in the System 7 days.

*I can see this conversation is just headed in the wrong direction ... -- MikeTrent *

----

Could somebody point me to some rules of thumb regarding Drawers? I recently began to refactor the Gui of my app, and as of this morning I have auxillary controls in a side-drawer for changing input parameters of my agents at runtime. It seemed a better place than they were before ( See OverComplexGuiSimplification ), though admittedly they're not visible in the screenshot on that page. I made it a drawer because I had too many panels, and the panel this resided in had too many states to show depending on the hilighted item in the outline view. here's the old setup:
http://www.home.earthlink.net/~zakariya/files/ComplexPanel.png

Now, the interface that originally was revealved in the panel when clicking on "Root", is instead always available in a drawer on the main window. It seems like an improvement to me, as sometimes I'd like to inspect aspects of a device in my simulated system at the same time as I'm controlling a behavioral input. 

But you all seem to have both experience and opinions, so I'd like to know if I did The-Wrong-Thing. 
http://www.home.earthlink.net/~zakariya/files/DrawerButIsItMad.png

--ShamylZakariya

----

Okay, in spite of my whining I am not a drawer expert. But I'll give you my (sollicited) opinion.

*I made it a drawer because I had too many panels, and the panel this resided in had too many states to show depending on the hilighted item in the outline view. *

One seemingly-appropriate way to use drawers here is to put the outline view in the drawer, and use the entire main panel view to display the controls for the selected item. That's very much like Xcode, iChat, Mail, etc ... you are displaying a list of items in a drawer, and they're influencing the kinds of controls that appear on the main viewer area. 

With this approach I assume the panel is an inspector of your graphic view. More on this in a sec.

*Now, the interface that originally was revealved in the panel when clicking on "Root", is instead always available in a drawer on the main window.*

This is not appropriate IMO because all you've done is stuck your controls in a junk drawer. One problem with this junk drawer solution is, as you have already discovered, it's not as scalable as you need. You basically have to keep adding junk drawers, and once you've done that you're destined for   the PerversionTracker hall of shame.

Going back in time a little to the **good** things to come from MOSX's past, I think the right way to expose these controls is through an inspector panel. Again, Xcode is one example, but see also Office, Photoshop, Omni*, etc. The inspector would be context sensitive to the front-most document window. If you want, shove your outline view into a drawer as noted above, so people can specify which collection of tools they want to manipulate (again Xcode like). If you really want to allow people to modify two documents at the same time (w/o clicking) you could implement a "Get Info"-like scheme, although personally I doubt that's really necessary.

Keeping the inspector separate has some advantages. People can position it where they want it it just stays there. They can close the window if they like. They don't constantly have to disclose/undisclose anything. You have a large area to work with. The user can easily resize the inspector as if it were a document window (if you choose) allowing for maximum flexibility. You could expose commonly used, recently used, or "overridden" controls on a main pane (again like Xcode) so the user has all of the most helpful stuff at their finger tips.

The NSDocument model was designed to accommodate things like multiple window controllers and what not. Your inspector should be no different than a normal window controller -- it's just it's document will change as the user activates other documents, and use the new document to re-initialize the window UI. I do this all the time and it works really well.

IMNSHO

-- MikeTrent

----

So Mike, you agree that MacJournal uses drawers correctly? Personally I think Drawers are a backwards step for Apple in terms of UI, with exceptions. Mail is a perfect example of when _to_ use drawers IMHO. iTunes and iPhoto are also examples where you could use drawers, but for obvious reasons they do not.

Having auxiliary controls is an absolute no, just as having toolbar items without associated menu items. Both are hideous crimes that seem to be appearing more and more all the time. 

Part of the blame lies with the Mac OS X HIG:

*A drawer should contain frequently accessed controls that don�t need to be visible at all times. A drawer�s contents should be closely related to the contents of its parent window.*

I think most of the issues stems from this. It needs to be reworded to:

*Drawers should contain information relevant to the parent window but not controls that affect the parent window.*

This would make apps like Mail and MacJournal correct, while hideous beasts like Starfish and other apps appearing on PerversionTracker would need to change their interface. If we look in the Mail drawer, they abide by this. We have information, an add mailbox button and an action button. Perfect. Nothing more needs to be in there. 

Then again, Drawers are a sketchy UI element that blend Inspector panels and the main window into a new element. It is definitely confusing after re-reading the HIG as to when you should use drawers, inspectors panels, toolbars or controls in the main window.

My two cents from someone still learning about good UID. -- MatPeterson

----

On a previous image there was another drawer on that same window [http://home.earthlink.net/~zakariya/files/TooComplex.png]. Are you planning to keep the second drawer? I can live with one drawer, but I can't stand two drawers on the same window. Perhaps that is just my dislike for drawers in general. -- RyanBates

You're right, & I agree. The trouble is, I have a lot of inspectors, and a lot of information that *must* be visible, and not enough screen, since I'm developing on a 12" PB. I wouldn't dream of having two panels on the one window. It's dumb. I was experimenting with having the behavioral inputs be on a side-panel and I wanted to see how it "felt". Probably it makes more sense to put the outline view on the side panel and have the behavioral inputs be in an inspector. I'm not 100% certain. What I do know is that two drawers is unacceptable, so if I do go for a side-drawer approach, the log & console drawer will have to be either made into a floating panel, or be a window-expanding-triangle-thingy.

The good news is, per my discussion in OverComplexGuiSimplification, I was able to shrink many elements of my gui a little bit at least; and the worst offender, the Force panel, is now less than half the size vertically and 3/4 size horizontally. That's good, at least.

This is very hard for me to figure out. --ShamylZakariya

*Harder than the complex C++ engine you made? ;) Seriously though, so many people underestimate the importance of good UI design, but you are taking the time to focus on it (which is good!). I have some more ideas to clean up the complicated panels, but let's steer that discussion back to OverComplexGuiSimplification. I don't think drawers is the answer, but I'm sure there are others who would disagree. -- RyanBates*

----

The point of a drawer is that you should be able to close it. 

In Mail you really can't, in iChat, if you have more than one group, you can't. You need the information in the drawer. I have yet to see a good example of drawer-usage that could not be solved better. Drawers are not completely useless, the problem is that there are few developers out there who have expertise in HumanComputerInteraction and interface design.

OmniWeb features an acceptable use of drawers: for the tabs. I can close it, even though I rarely do since there is nothing else that tells me that the current window has more than one drawer (save for the dot in the close button). When I only have one tab, it is closed, when I have more than one it is open. It is not good, but acceptable. In older versions of OmniWeb, the bookmarks were in a drawer, that was good design. I could open it when I needed it, and it didn't have to stay open.

-- TheoHultberg/Iconara

----

I agree completely about not using a drawer for something that the user needs permanent access to (I don't care for Mail's implementation). On the other end, if it is something that only needs to be accessed once very quickly, it should not be placed in a drawer either (such as OmniWeb's history drawer). I hated having to open it and close it every time I needed to quickly access the history. So perhaps drawers are better suited for something that will need to be accessed temporarily, but not quickly for only one thing.

As for OmniWeb's bookmarks drawer, I think it requires too much user interaction. I found it awkward to rename and organize the bookmarks. Drawers should be kept simple, like the tab drawer. But (because I can find something negative in almost everything) I do not think drawers belong on any web browser because it makes it difficult to browse wide web pages at 1024x768 screen resolution.

To sum it up, I prefer drawers if they do not take very much user interaction, are simple, and temporary (but not too temporary). -- RyanBates

----

If anybody's still listening:

I am working on a relatively simple doc-based app with a table view and toolbar constituting the entire content area of the document.
The table's row-oriented operations are handled by the toolbar, but I want to give the user a convenient set of tab-able text fields
to enter data into the cells. The user can hit �return� in the last text field to commit the data to a new row via an action connected
with that text field. Is it really so dreadful to put the data entry form into a drawer? Certainly, the table is going to be used for far more
than adding and deleting data (I mean, it's not just a tutorial app on table views and toolbars).

Anyway it seems to me that clicking a disclosure triangle to expose a subsidiary interface is ergonomically-equivalent to operating a drawer.
My toolbar has a button to toggle the drawer open and shut.

----

Out of curiosity, why not just make the table view editable? Also, how complicated would the controls be in the drawer?

One primary difference between drawers and disclosure triangles is a drawer is outside the bounds of the window. I personally think windows with drawers are more awkward to work with because I expect a window to be completely enclosed in the rectangle below the title bar. Drawers go against this and therefore make the shape of the window more complicated.

With that in mind, you must not only consider the drawers contents but also how the user will interact with that window. Will the user most likely have more than one drawer-window open at a time? Will the user be constantly switching between other windows while using that one? Will most users keep your application open all the time (such as Mail)? All of these things must be taken into consideration.

-- RyanBates

----

I initially made the choice because it seemed more efficient to enter data using a type-tab-return scheme. Of course, I made the table editable
so that individual cells can be corrected (for spelling, or in the case of numeric apps, if the numbers entered are wrong). The type-tab-return
is IMHO a bit more efficient when transcribing large amounts of data...Of course I can tab between cells of existing rows.

Giving the "Add row" command a keyboard equivalent (as it should have) permits almost the same ergonomics, but then you have the sequence:

"type; tab; CMD-(whatever)" rather than "type; tab; return" -- I think the latter wins by a neck rather than just by a nose. But Secretariat, it aint.

Now throw in the ugliness of the drawer sticking out there like a, hmm, well...never mind. Yes, the drawer is ungainly.
But certainly no uglier than having a block of text fields in a space below the table view. Now THAT's ugly!

I expect the user to create a document, and populate it with at least 1-2 dozen rows before really needing to use the other features of the document.
Once the user has a working document, which could have as many as 100 - 500+ rows, the need for the drawer will diminish to near-insignificance
and it can be mostly put away.

It is possible, even likely, that the user will want more than one of these windows open at a time, in the data-entry phase of using the document, but
particularly in the analysis (or study) phase.

The app behind this UID can be anything from a vocabulary drill to a table of numeric data for which the user wants to collect various kinds of
column statistics or correlations between two (or among several!) columns (not just the kind that spreadsheets do!)

For the vocabulary drill, the inputs are just two text fields - very simple drawer layout. For math, there might be a dozen or more columns.

I guess it would be cleaner to implement the input interface as a sheet - could the input sheet just run modally with the user tabbing between
its fields and then hitting "return" to add rows, and then dismissing the sheet with a button? (It's just that the key equivalent of the dismiss
button could no longer be "Return". Is that less of a UID sin?)

Unfortunately here, I ALREADY use two sheets on the document window - one to confirm deleting a row and the other for preferences related to
the individual document, rather than the app as a whole. HMMMM. Document prefs could go in the drawer - a seldom-used set of controls...

Thanks for the input, Ryan. It really got me thinking about this a little more carefully. 

*Note added after further study: Now I see that I can have as many sheets as I want, if I just use

    [ NSApp beginSheet: dataEntryPanel modalForWindow: win modalDelegate: del didEndSelector: end contextInfo: inf ];

dispensing entirely with the drawer.*

----
I hope the drawer bashers are happy.... now (10.4) Mail doesn't have a drawer for mailboxes.   Now we have this.... this... abomination.    Apple has now duplicated the functionality of a drawer in a custom pane.  Compared to the old drawer implementation, I think this new implementation is terrible.    I often spend a lot of time with the mailboxes hidden, and just want to expose them briefly to look for new mail my filters have sorted into other mailboxes.   Drawers were great because you could toggle them, take a peek, toggle them back.  In the new, non-drawer, implementation, when you hide the draw... um... pane... the toolbar icons shift over.  So to simply toggle the drawer, you have to move your mouse around and find the icon.  This feels very strange and wrong to me.   Now, instead of just a  drawer opening and closing, you have the entire contents of the window, controls and all,  shifting back and forth.  DancingToolBarControls.    Not an improvement, in my view.    I think the drawer bashers are just wrong in some cases.   Mail is one of them.  Kolmogorov  mailto:oxygenduck@yahoo.com"

*Although this isn't a complete solution, removing the flexible space on the far left side of the toolbar will keep the icons on the left, so they won't bounce around when hiding/showing the mailboxes. You can also replace it with a few fixed-width spaces if you like the space there even when the mailboxes are hidden. Also, command-shift-m will toggle the pane, which might help too.*

