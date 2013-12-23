---
layout: page
title: UINavigationBar
---



Part of the iPhone General/UIKit framework. Subclass of General/UIView.

This is that handy navigation header that goes on the top of views that can be.. navigated.

Button Styles: 0 = Normal, 1 = Red, 2 = Back Arrow, 3 = Blue

    
- (void)navigationBar:(General/UINavigationBar*)navbar buttonClicked:(int)button 
{
	switch (button) 
	{
		case 0: General/NSLog(@"left"); break;
		case 1:	General/NSLog(@"right"); break;
	}
}

- (void)applicationDidFinishLaunching:(id)unused
{
	window = General/[[UIWindow alloc] initWithContentRect:General/[UIHardware fullScreenApplicationContentRect]];

	//Setup main view
	struct General/CGRect rect = General/[UIHardware fullScreenApplicationContentRect];
	rect.origin.x = rect.origin.y = 0.0f;
	mainView = General/[[UIView alloc] initWithFrame:rect];
	[window setContentView:mainView];

	//Setup navigation var
	navBar = General/[[UINavigationBar alloc] initWithFrame:General/CGRectMake(0.0f, 0.0f, 320.0f, 40.0f)];
	[navBar showButtonsWithLeftTitle:@"Left" rightTitle:@"Right" leftBack:NO];
	//you can also do something like [navBar showLeftButton:@"Left" withStyle:1 rightButton:@"right" withStyle:0]; <-- this will let you change the button color!
	[navBar setBarStyle:5]; // This sets the color and look of the navigation bar.
	[navBar setDelegate:self];

	[mainView addSubview:navBar];

	[window orderFront:self];
	[window makeKey:self];
	[window _setHidden:NO];
}


----

**Methods**

<code>- (id)initWithFrame:(General/CGRect)frame;</code>

Designated initializer!

<code>- (void)showButtonsWithLeftTitle:(General/NSString*)left rightTitle:(General/NSString*)right leftBack:(BOOL)flag;</code>

Sets the title of the left and right buttons, and specifies whether the left button has the "back" styling. If a title is nil, its corresponding button is not shown.

<code>- (void)setDelegate:(id)delegate;</code>

Delegate receives clicks.

<code>- (void)setBarStyle:(int)style;</code>

0 - Normal Style 

1 - Dark Style 

2 - Dark Style, Semi-transparent

3+ Ignored, assumed Normal Style


----

**
General/UINavigationBar delegate methods
**

<code>- (void)navigationBar:(General/UINavigationBar*)bar buttonClicked:(int)which;</code>

0 for left, 1 for right.

NOTE: my demo app is returning 1 for left, 0 for right.
