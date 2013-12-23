---
layout: page
title: FirstResponderSetAfterDelay
---

I am really going crazy over this...

I have a nib file containing my 'document window'. I have set the first responder outlet of the window to some view, but in my NSWindowController subclass it is nil when printed, and when my window opens, the view in question does not have focus.

If I set the first responder myself like this:

    
   self window] makeFirstResponder:[self documentView;


Then it doesn't work, but if I instead do this:

    
   self window] performSelector:@selector(makeFirstResponder:)
      withObject:[self documentView]
      afterDelay:0.0];


Then it *does* work... so who is setting the first responder *after* invoking init of my [[NSWindowController subclass? (for the records, I do invoke [self window] before this, to get the nib file loaded).

----

You want to place the first code snippet in your window controller's -windowDidLoad method.  At that point, all your nib's objects and connections between them will be set up properly. -- Bo

PS However, I should point out that in your nib file, your window has an outlet called initialFirstResponder.  If you set that to your documentView, you don't have to write any code to get the desired effect.

----

Thanks for the hint, but it was actually the initialFirstResponder outlet I had connected (which I referred to as 'the first responder outlet of the window').

If I add:

    
- (void)windowDidLoad
{
   NSLog(@"%@", self window] firstResponder]);
}


Then it prints the name of my window class (despite the outlet being set to my document view). However, I can makeFirstResponder: in windowDidLoad w/o the delay, so this is an improvement nonetheless :-)

----

There seems to be more to it than just that...

My set-up is that I have created an [[NSWindow in IB. It contains an NSTabView and this is actually what I have set as initialFirstResponder. In my program I add a new NSTabViewItem with my document view, and I set the initialFirstResponder of this NSTabViewItem to my document view, so I'd hoped it would propagate down -- but according to the NSLog() the window is first responder and my document view never gets focus.

But if I don't add any NSTabViewItem during startup, then whatever object I place at the first tab page will get focus (even though the NSLog() states that it is the window which is first responder).

So the problem doesn't seem to be with the initialFirstResponder outlet (as I first thought), but rather that if I add an NSTabViewItem to my NSTabView during startup, then the object set as initial first responder for this tab page, won't receive focus.

I can't really say I understand any of this... if I do this (in windowDidLoad):

    
   [self newTab:self]; // will add the tab
   self window] makeFirstResponder:[self documentView;


Then my document view does *not* get focus (after startup).

If I do nothing, whatever gadget is on the first tab will get focus.

If I instead do this (again in windowDidLoad):

    
   [self performSelector:@selector(newTab:)
      withObject:self
      afterDelay:0.0];


Then my document view *will* get focus... but unfortunately it will flicker (displaying the first tab page before the new one is added).

So clearly something is going on *after* windowDidLoad, which messes with the first responder...

----

Whoops, I had missed the 'first responder of the window' part.  I'd really have to see the code for your -newTab: method to diagnose this, but my first guess is that you're not bringing the document's tab view item to the front before trying to make its documentView item the first responder.  Only the frontmost tab's subviews are part of the window's view hierarchy, and thus only those can become first responder. Try replacing your -makeFirstResponder call with a call to the tab view -selectTabViewItem: method to select the document's tab. -- Bo

----

Here's the involved code:

    

- (void)windowDidLoad
{
   self window] setDelegate:self]; // I thought [[NSWindowController would setup this?
   [self newTab:self];

   // this is currently the only working solution
   self window] performSelector:@selector(makeFirstResponder:)
      withObject:[self selectedDocument]
      afterDelay:0.0];
}

- ([[IBAction)newTab:(id)sender
{
   if([self window] && TabView)
   {
      NSTabViewItem *tabViewItem = [[NSTabViewItem alloc] initWithIdentifier:0];

      id textView = [[OakTextView alloc] init];
      NSScrollView *scrollView = [[NSScrollView alloc] init];
      [scrollView setDocumentView:textView];
      [scrollView setHasVerticalScroller:YES];
      [scrollView setHasHorizontalScroller:YES];
      [scrollView setDrawsBackground:NO];
      scrollView contentView] setCopiesOnScroll:YES];

      [scrollView setAutoresizingMask:[[NSViewWidthSizable | NSViewHeightSizable];
      [tabViewItem setView:scrollView];
      [tabViewItem setInitialFirstResponder:textView];
      [tabViewItem setLabel:[textView displayName]];
   
      int index = [TabView indexOfTabViewItem:[TabView selectedTabViewItem]] + 1;
      [TabView insertTabViewItem:tabViewItem atIndex:index];
      [TabView selectTabViewItemAtIndex:index];

      [textView release];
      [scrollView release];
      [tabViewItem release];
   }
}



----

It looks fine.  When I dumped this code into my testbed app, it worked like a charm after the following changes:

*changed the OakTextView to an NSTextView.  purely a formality.
*changed the -init calls for your view subclasses to -initWithFrame:. This is mostly a formality, except that the NSTextView doesn't get automatically resized and so won't appear unless non-zero frame is supplied.
*using a constant string instead of [textView displayName] for the tab label.  Also a formality.
*deleted the whole -performSelector:...:afterDelay: method entirely. 

End result: the tab appeared selected and the text view within was the first responder exactly like it should be.

I'd guess that the problem is somewhere else, perhaps either in your connections (though I can't think of a possible configuration that would cause the symptoms you describe), in your OakTextView (perhaps it isn't immediately ready to become first responder for some reason?) or in some other processing you're doing after your window loads.  -- Bo

----

Okay, thanks a lot for going through this! it made me look elsewhere, and it seems I have found the problem. In System Preferences / Keyboard I have enabled Full Keyboard Access: For windows and dialogs, highlight: Any control.

If I change this setting to "Text boxes and lists only" then it also work on my side.

I will try to make a simple app which demonstrate this and see if Apple will acknowledge it as a bug, perhaps you can verify that your test application also breaks if you set it to "Any control"?

And again, many thanks for your help in this matter!

----

Yep, that seems to be the problem alright; good catch. -- Bo

