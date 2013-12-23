---
layout: page
title: UIPushButton
---



From http://ellkro.jot.com/General/WikiHome/iPhoneDevDocs/General/UIPushButton

    
//Button images - in application bundle.  These are from the clock app
General/UIImage* btnImage = General/[UIImage imageNamed:@"button_green.png"];
General/UIImage* btnImagePressed = General/[UIImage imageNamed:@"button_green_pressed.png"];

General/UIPushButton* pushButton = General/[[UIPushButton alloc] initWithTitle:@"My Button!" autosizesToFit:NO];
[pushButton setFrame: General/CGRectMake(100.0, 130.0, 100.0, 50.0)];
[pushButton setDrawsShadow: YES];
[pushButton setEnabled:YES];  //may not be needed
[pushButton setStretchBackground:YES];

[pushButton setBackground:btnImage forState:0];  //up state
[pushButton setBackground:btnImagePressed forState:1]; //down state

