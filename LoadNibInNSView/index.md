---
layout: page
title: LoadNibInNSView
---

I'm trying to load the content of a nib file in order to add it to a NSView of my MainMenu.nib
So there is two nib files in my project : MainMenu.nib and Box.nib.
My MainMenu nib contains an NSObject subclass myController; a simple button (with an add: action) and a NSView myCustomView.
I tried this without success : 
    
#import "myController.h"

@implementation myController

- (IBAction)add:(id)sender
{
	NSBundle *myBundle = [NSBundle mainBundle];
	NSNib *boxNib = [[NSNib alloc] initWithNibNamed:@"Box" bundle:myBundle];
	NSView *aView = [[NSView alloc] init];
	[boxNib instantiateNibWithOwner: aView topLevelObjects:nil];
	[myView addSubview: aView];
	[aView release];
	[boxNib release];
}

@end

If i try to draw directly to myMiew using [boxNib instantiateNibWithOwner: myView topLevelObjects:nil]; it dont change anything.
What is wrong? It dont sends any error message but myView is blank. - StephaneDassieu


----

I don't know if your code is supposed to work (I've never done it that way) but I usually load nibs using     [NSBundle loadNibNamed:(NSString *)nibName owner:(id)nibOwner]

[http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSBundleAdditions.html]

Try not releasing boxNib too.

----

    aView is a plain NSView.

    aView is set as the nib's owner. This means that a plain NSView is going to be the File's Owner object that you see in the nib file.

It's blank because you're not doing *anything* to put anything into     aView. Loading the nib isn't changing     aView at all, so there is no reason why this code would work.

In order to do this, you'll need a controller class with outlets to the interesting bits in the nib. This can either be your main controller or a specialized controller, depending on the situation.

