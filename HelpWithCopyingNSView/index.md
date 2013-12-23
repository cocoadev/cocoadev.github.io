---
layout: page
title: HelpWithCopyingNSView
---

Hello there, I'm having trouble being able to copy an NSView, here is the code i've tried:
 .h
 @interface TRTabbleView : NSTableView
 {
 	IBOutlet WebView * webview;
 	IBOutlet TRContentView * contentTemplate;
 	IBOutlet NSWindow * window;
 ........
 .m
 - (void)awakeFromNib {
 	webpages = [NSMutableArray new];
 	
 	TRContentView * homepageView = contentTemplate;
 	[homepageView loadPage:@"http://www.google.com"];
 	
 	NSDictionary * homepage = [NSDictionary dictionaryWithObjectsAndKeys:
 		homepageView, @"View",
 		@"Title 1", @"Title",
 		nil];
 	
 	[webpages addObject:homepage];
 	
 	//////////BEGINTEST
 	TRContentView * page2 = contentTemplate;
 	[page2 loadPage:@"http://www.slashdot.org"];
 	
 	NSDictionary * anotherPage = [NSDictionary dictionaryWithObjectsAndKeys:
 		page2, @"View",
 		@"Title 2", @"Title",
 		nil];
 	
 	[webpages addObject:anotherPage];
 	//////////ENDTEST
 .....

Basically I want to use the NSView in interface builder as a template and then copy that to all the 'tabs' i want, I want to use NSViews not TabViewItems. The problem is they end up pointing to the same object, so I get the two views I want, and they work in two tabs, just they show the same object. I've tried [contentTemplate copy] to which it doesn't respond...  I'm out of ideas, anyone want to shed some light on what I'm doing wrong, I've never done something like this.

Thanks!

----

The easiest way to do this kind of template thing is to put your template NSView in a separate nib and just load the nib every time you need a new view. The new NSNib class can facilitate this sort of thing. The other way to "copy" a view is to pass it through an archiver, like so:
 NSData *tempData = [NSKeyedArchiver archivedDataWithRootObject:myView];
 NSView *copiedView = [NSKeyedUnarchiver unarchiveObjectWithData:tempData];
Neither of these solutions is going to be incredibly fast, but they'll work fine. The nib approach will allow you to have connections to the view, which will probably make life easier.

