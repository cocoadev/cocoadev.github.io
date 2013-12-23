---
layout: page
title: ITunesSearchFilterBar
---



I'm trying to create the search filter bar that exists in both iTunes and Mail.  I guess it can be done with an NSView with a background gradient, along with some custom NSButtons, but I was wondering if anyone knew of an easier/different way to accomplish this.  Even a tutorial or example somewhere online would be great (I searched around google and cocoadev for a while, finding nothing).  Thanks! http://goo.gl/OeSCu

http://burgundylogan.com/media/iTunesSearchFilterBar.jpg (just a pic for reference)

-- LoganRockmore

----

Well, you don't have to make custom buttons if you're using Tiger or newer. In Tiger, NSButton.h adds to the NSBezelStyle enum two new values: NSRoundRectBezelStyle and NSRecessedBezelStyle. I forget which is which, but neither are available from InterfaceBuilder and one of them draws those buttons.

Other than that, yeah, you're probably going to have to build it yourself. Apple provides no classes to do it for you (NSMatrix might help though, but I doubt it.) -JonathanGrynspan

----

Alright, so I've started to get this going... I've got an NSView with a custom background image (just a screenshot of the iTunes gradient), and I've got some buttons set up on top of it.  However, when the button isn't selected, the striped background of the window shown through, and not the NSView's background.  I like how the button doesn't show any border when it isn't selected, and it actually doesn't change if the mouse hovers over (if I wanted to really replicate the original iTunes/Mail button, this would happen, but I haven't gotten that far yet, and I don't think I really mind it if I never impliment that).  Any idea why this is happening?  Thanks.

http://www.burgundylogan.com/media/FilterBarButtonBackground.jpg

Here is how I currently set up any given button...
    
NSButton *newButton = [[NSButton alloc] init];
[newButton setTitle:@"button title"];
[newButton setButtonType:NSPushOnPushOffButton];
[newButton setBezelStyle:NSRecessedBezelStyle];
[newButton sizeToFit];

// Locate the Button using X,Y Coordinates... code omitted

[view addSubView:newButton];
[newButton setShowsBorderOnlyWhileMouseInside:YES]; 


-- LoganRockmore

----

Try newButton cell] setBackgroundColor: [[[NSColor clearColor]];

- FranciscoTolmasky

----

Unfortunately, with the clearColor, it is now showing up like this:

http://www.burgundylogan.com/media/FilterBarButtonBackground2.jpg

Maybe it would help if I also provided how I added the background image:
    
NSImage *backgroundImage = [NSImage imageNamed:@"FilterBarBackground"];
[backgroundImage compositeToPoint:NSMakePoint(0,0) operation:NSCompositeCopy];


-- LoganRockmore

----

I still have not been able to get this Filter Bar to work correctly.  I'll provide a link here to a beta version of the program, where you can see the error in action.  If any body can help me out with this, that would be great.  Thank you so much.  

-- LoganRockmore


----

Have you seen the AMRollOverButton available from harmless.de? http://www.harmless.de/cocoa.html

----

It must be possible with Cocoa because there's a filter bar just like you're trying to build in Acquisition, a Cocoa app.  Screenshot of the filter bar: http://www.newsfirex.com/blog/?p=109

Wish I knew more about how to do it because I want to build one also.

----

I have recently gone fishing in the iTunes rsrc file and it appears that they make that search bar entirely with images, since all the appropriate images  are embedded inside that rsrc file.

----

I just ran into AMRollOverButton last night, and I tried it out.  I haven't yet gotten it to work quite yet, but it's definitely a possibility, especially since the Bezel used for these buttons isn't available before 10.4, leaving older users with simply text and functional buttons, but no cool bezel look for the buttons.

I'll definitely send an email over to the developer of Acquisition, and see if he'll send me some insight as to how he successfully created it, and if it's compatable with earlier version of OS X.

And I also did go fishing a little bit, but I was scrougning around Mail.app, looking through all the .nibs to see if there was anything I could find, with no success.  However, I did not look through every single one, so there might still be some hope.  That's very interesting that the iTunes bar is made of all of the images.  I'm not sure that piecing together the buttons would work.  It would be easier to simple use the AMRollOverButton.

Thanks everyone for the help.  I'll let you know what I hear from the developer of Acquisition, and if I find anything new.

- LoganRockmore

----

The AMRollOverButton has been available for several years - well before Tiger. Perhaps you've misunderstood the documentation?

----

No, I got that AMRollOverButton is a possibility.  I was talking about the NSRecessedBezelStyle, which is an NSBezel than can be applied to NSButtons.  This is what I'm using in the above pictures.  However, NSRecessedBezelStyle is not available before 10.4, and just shows up as text, with no button background, but the button's actions still work.  This is why AMRollOverButton is probably a good idea, since it would work on earlier versions.

- LoganRockmore

----

Has anyone solved this problem? I am trying to create a filter bar just like iTunes and I am getting the same results. The button is trying to draw the background behind the image. What can be done to change that behavior.

----
I think you want the source-over compositing operation, not copy. --BenStiglitz

----
Logan, did you find a solution ? I have the same issue with NSReccessedStyle button's. Tried everything: setting cell background image, cell background color, subclassing -drawInteriorWithFrame, -drawWithFrame, � no sucess.
I can't believe this has not been solved by someone.

----

Did you try subclassing NSButton and overriding -isOpaque to always return NO? -JonathanGrynspan

----

Have you tried setting the color to nil?  I did this with NSBrowserCell which was overdrawing on my nice gradient (thanks Shipley) and it worked fine.
    
- (NSColor *)highlightColorInView:(NSView *)controlView
{
	return nil;//[NSColor clearColor];
}

Course that's the wrong class but it should work for you too.

*It's also possible the black is being caused by the button thinking that it     -isOpaque -- JediKnil*

----

Ok Folks, it's 12:30 so I just can't go on.  This is what 4 hours has given me, and the gauntlet is down!  Who can finish this class before I do?  Here's the primer:
I started a new project.  downloaded the school planner app, opened up its mainMenu.nib, and dragged the FilterBar view from its window over to my window.  Then I closed the old project.  So, logan, it's the same name at the top level so you can just drag my stuff over there if you want.
I copied all the code snippets from here and plugged them in, and then started whipping away at it.  I ended up having to subclass, because the cells all want to vary their state according to two main variables: selection and mouse presence.  Try as I might, I could not find a way to get the cells to track the mouse without subclassing them and having the filterbar view keep tracking rects on them.

The classes therefore are 
filterbar (an NSView), 
it's button SDFilterBarButton (an NSButton) 
and its cell SDFilterBarButtonCell (an NSButtonCell)

What this class does as of today:
Draws pretty buttons. (The text attributes are a bit off, though.  T&E).

What it doesn't do (yet)
Groupings of cells, with mutual exclusion optional.
delegate notification of selection change (though with bindings just kyPath:buttons.representedObjects should work, after I fill in much of the relations.  It's still VERY skeletal as far as backend goes)

But that's all utilization stuff, and I've just been trying to kill that graphics bug.  If you're not wanting to parse the code to figure out what i did, the short answer is that I overrode the ButtonCell method drawBezelWithFrame: to NOT draw anything if it's neither selected not mouseovered.  Since this method is seperate from drawTitle:, the title gets drawn and the image too.  Beaners!

Anyway, I'll keep updating this code tomorrow, or wednesday depending on whether or not my dealer is in town tomorrow.

SNIP!  ...code sent to SearchFilterBarSampleCode

-stephen
----

Ok I'm actually coming along pretty fast with this.  I have it correctly displaying buttons for mouseover, mousedown, and selected, and it distinguishes between an active and non-key window.  And there are three styles to select from - generic/transparent (finder, safari, for textured windows), itunes style (silver) and mail style (blue).  That's, what, 18, possibilities?  And it's fast!

I think I'm just gonna make an IB palette, since it looks like this might really work as it should, and it feels pretty stable and clean.  I have to learn how to do that though, so give me a few days.  I am still trying to decide how to implement its relationships re: delegate / searchitem, etc.  The trouble is I kinda jumped the gun and my own project isn't ready for this element yet, so I don't really know how search predicates work in Core data et al.  I don't want to configure this to work one way and then find that it's way incompatible with Bindings.  We'll see.

But it doesn't look like anyone here is frothing at the mouth anyway...

PS - to anybody who maintains the site, don't worry - I'll clean up all this jibber-jabber post-facto.

Stephen

----

Hey guys, I didn't even know that this topic was resurrected recently.  Stephen - it looks like you have made some headway.  I did get a finished product (and by finished, I meant that I was satisfied with the result.  I'm actually putting it in my Assignment Planner <http://assignmentplanner.burgundylogan.com/ > application and releasing it within the next few days.  However, there still is no background image.)  I had originally planned to package it together and release it so anyone can use it, and I don't know what happened... I guess it just got delayed and I never got back to it (you all know how it is).  But!  I found my code, and I'll attach it here.  Take a look at it, and see if you guys have any suggestions.  I still plan on releasing it through my means so that anyone can use, but I would love to make this a CocoaDev collaborative effort.  Thanks!

Download Here -- http://www.burgundylogan.com/Code/LRFilterBar/

-- LoganRockmore

PS:  I also tried to start an IB Palette!  However, that's where I stopped.  No reason for the stop, it seemed feasable.

----

I just got some email help from Baron Stephane and I was able to get the background coloring to work to a certain degree.  The FilterBar now looks like this:

http://www.burgundylogan.com/media/FilterBar1.1Coloring.jpg

I'm happy enough with it to use it in the program, but there are a few quirks.  For example, the original iTunes bar and NSRecessedBezel have a different image for hover and click, whereas this FilterBar doesn't.  Also, when you click any button, there's a quick blink between the hover and the click image.  I'm not sure if these things are fixable.  Anyways, you can give it a try here.  Thanks!

Download Here -- http://www.burgundylogan.com/Code/LRFilterBar/

-- LoganRockmore

----

Just wanted to add that I recently created my own implementation, called AMButtonBar:

http://www.harmless.de/cocoa-code.html#buttonbar

It's all custom drawing, has different representations for all states (hover, selected, unselected, disabled, hover+selected) and uses only a single control with multiple cells.

It does *not* do (overflow) menus or separators however.

-- Andreas Mayer

----

I'll add one more alternative to the mix.  Mine's called SRScopeBar, uses multiple groups, labels, separators, automatic resizing, collapsing, an overflow menu, an accessory view, etc and it comes with an IB3 plugin and is bindings compatible.  Check it out at http://www.flytocode.com

- Sean Rich

