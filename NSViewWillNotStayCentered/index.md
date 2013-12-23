---
layout: page
title: NSViewWillNotStayCentered
---

Using InterfaceBuilder I am creating a standard document window and placing an NSView in the middle ( absolute size, board is dynamic ). I would think the NSView would stay centered when I resize the window. It comes up first centered horizontally, but locked to the top of the window, if I resize the window horizontally smaller than the view the view locks to the right side of the window ( locked to the upper right ).

Found and tested the Sproing example from the Apple Developer Sample Code. It has the same problem.

----

I haven't dealt with this in any of my applications, but I imagine a good place to start would be to set the minimum size of the window to something larger than your NSView subclass, or put it in an NSScrollView the size of the minimum window, and center that, changing the size of the scrollview when needed.

----

I actually am trying to create a basic canvas with a resizable window ( contents can be of arbitrary dimensions ). Nothing I have seen makes me believe this is possible with NSView. Seems like it should work, but doesn't.

----

*I've never seen any problems with this. Your custom view should be centered in the window, and only the two inner springs should be activated. The window should **not** be smaller than the view itself. If you want scrolling, select your view and choose Layout > Make subviews of > Scroll View, then choose Layout > Size to Fit on your custom view. This should "just work", but I'm not exactly sure what your problem is. Also, sometimes setting the minimum size of the window to be a more sensible value than 167 by 5 helps a bit. --JediKnil*

----

Why couldn't the window be smaller than the view? That is the point. It is a canvas viewport. This what NSView promises ( even in the Sproing example ). Ironically the Sproing example includes a 'Recenter' function in case the view gets off center. Why would this be needed? Is there an easy fix for this irregularity? Should I just be writing my own subclass of NSView in order to make it work as advertised? Sorry, just frustrated. Seemed like this was a no-brainer initially.

----

I think looking into NSWindow's delegate methods (like -windowDidResize:) and then resizing your views (you'll need to do frame and bounds if you want it clipped when the window is smaller than the view) programmatically within that would be good. Also, don't depend on the window's rect's size matching the window's content view's frame's size, because it'll break if you use resolution switching (which can be enabled in Quartz debug.) Instead, convert the rects using the -convert... functions in NSWindow and NSView.

----

**Centering A Custom View Within An NSTabView**

I'm having trouble centering a custom view within a NSTabView item. Here's the hacked up section where I am doing this:

    

-(void)awakeFromNib
{
   NSMutableDictionary * mods;
   NSEnumerator *enumerator;
   NSString * key;

   register_modules();

   mods = guimod_getdict();
   enumerator = [ mods keyEnumerator ];

   while(( key = [enumerator nextObject] ) )
   {
      TestView * viewobj;
      NSTabViewItem * tabitem;

      viewobj = [ mods objectForKey: key ];

      tabitem = [[NSTabViewItem alloc] initWithIdentifier: key ];

      NSView * view = [ viewobj getView ];

      unsigned mask = NSViewMaxYMargin | NSViewMaxXMargin |
                                      NSViewMinYMargin  | NSViewMinXMargin ;

      [ view setAutoresizingMask: mask ];
      [ tabitem setLabel: key ];
      [ tabitem setView: view ];
      [ tabView addTabViewItem: tabitem ];
   }
}



I figured that setAutoresizingMask was what I needed, but it's not helping at all.  

----
Your problem is not that the view is off-centered, it's that its contents are. When you call     setView: on the tab view item, the *entire tab* becomes your view, and the items within it have their natural autoresizing behavior. In IB, change your items' autosizing preferences to make this work. (Just out of curiosity, why can't you just put the items in the tab view in IB? Does it have to be dynamic?) --JediKnil

----

Thanks for the quick response.  I've played around with the autoresizing stuff in IB, nothing seems to work.  Perhaps I need to insert a view into the NSTabViewItem to fill up the space, and then insert my custom view?  I'll try that now and get back if it works.

I have to dynamically create them because I have a bunch of modules that may or may not need to be displayed.

... time passes ...

I got a little closer by adding my view as a subview of the one created for me when I allocate a NSTabViewItem.
The only difference from the above code is that instead of

    
      [ tabitem setView: view ];


I have

    
     [ [ tabitem view ] addSubview: view ];


----

You could try putting everything in the NSView into another NSView (possibly an NSBox) and then have that view's autoresizing mask set to     NSViewMinXMargin | NSViewMaxYMargin.

----

That isn't working either.  I have tried so many things, I just can't get the damn thing to center.  I'm sure this is a basic issue, but it's really hanging me up now.  

----

*I don't see where you explicitly set the view's frame's origin to 0,0. It might not be the problem, but it's worth a try.*

    
NSRect viewFrame = [myView frame];
viewFrame.origin = NSMakePoint(0,0);
[myView setFrame:viewFrame];


----

I understand that you are adding the tab items programmatically, but don't see that it should impact your ability to set the
behavior of each tab item's view. Each tab view item is built to sit within the size of the tab view that you create.

If you know ahead of time what the contents of each custom view are going to be, then you know how big the superview of
those controls has to be. If the views in the different tabs are of various sizes, you probably have to make the tab view
big enough to hold the biggest of these, seeing as how the user will want to tab between them without resizing the window.

If you put your widget controls in a box subview and center that box in your custom view, you should get what you want,
as long as you set the autosizing behavior of each subview to remain centered in its superview (in other words, give your
subviews a constant size.

I am doubtful that you are using the right resizing mask to achieve what you seem to want, which is centering within each tab.

Probably the one you want is NSV**'iewNotSizable.

If you don't know before runtime what controls your custom view is going to have in it, you are in a world of hurt, my friend.
Instrument panels just don't act that way. I don't think that L**'abView tries anything like that. It can be done, but you
have lots of work to do.

I constructed a test project consisting of a tab view, an instance of a custom view laid out in IB with two labeled text fields and a button

I added the widgets to a box subview and set the autosizing behavior of the box **to remain centered in its superview**

I visually **center** the box subview in the custom view (you can do this exactly if you want); if you arrange the widgets
in the box subview to suit your aesthetic whim, you should get an appearance that pleases you.

I set the autosizing behavior of the *custom view* also to remain centered in its superview (which happens to be the view of a tab view item)

For simplicity's sake I set the autosizing behavior of the tab view to remain centered in its superview, the window.

I simply add the custom view (cView, below) to the first tab item in awakeFromNib

    
      [ [ tabView tabViewItemAtIndex: 0 ] setView: cView ];
	  NSTabViewItem *tvitem = [ [ NSTabViewItem alloc ] init ];
	  [ tvitem setLabel: @"Item" ];
	  [ tabView addTabViewItem: tvitem ];
      [ [ tabView tabViewItemAtIndex: 2 ] setView: cView ];


My version of this app demonstrates that adding the custom view to a new tab view item gives the same behavior.

The result is very much like what you seem to want, except that you also seem to want some other things too.
That has mostly to do with what you call your "modules.
If you have to add more than one module to a single tab view, centering is going to be a challenge.

OTOH, if you figure out how to set that resizing mask programmatically, you should be able to get what you want.

----

For now, I can assume that all of the views from the plugins will fit into the NSTabView.  If I have to deal with NSScroller later, I will, but I should definitely get this issue worked out first.

I can't statically create all of the tabs (even with custom views in them) in IB because I don't know how many plugins will be available/loaded. (hmm... maybe I can create 1 in IB, and _copy_ that one when I'm creating the new tabs? )

I think I've exhausted all of the autoResizingMask options too... I've been hacking away at this for too long, I'm gonna just let it be for now.  If I ever figure it out, I'll be sure to post it here, if not.... well, I may look into the cullinary arts.

    

   while(( plug = [enumerator nextObject] ) )
   {
      NSTabViewItem * tabitem;
      unsigned mask;

      tabitem = [[NSTabViewItem alloc] initWithIdentifier: plug ];

      NSRect frame = [ tabView contentRect ];

      NSView * view = [ [ NSView alloc ] initWithFrame: frame ];

      [ view addSubview: [ plug getView ] ];

      [ tabitem setView : view ];

      [ tabitem setLabel: [ plug getName ] ];

      [ tabView addTabViewItem: tabitem ];

   }


