---
layout: page
title: UIAlertSheet
---



    
// Alert sheet attached to bootom of Screen.
General/UIAlertSheet *alertSheet = General/[[UIAlertSheet alloc] initWithFrame:General/CGRectMake(0, 240, 320, 240)];
[alertSheet setTitle:@"Alert Title"];
[alertSheet setBodyText:@"This is an alert."];
[alertSheet addButtonWithTitle:@"Yes"];
[alertSheet addButtonWithTitle:@"No"];
[alertSheet setDelegate:self];
[alertSheet presentSheetFromAboveView:self];


    
// Allert sheet displayed at centre of screen.
General/NSArray *buttons = General/[NSArray arrayWithObjects:@"OK", @"Cancel", nil];
General/UIAlertSheet *alertSheet = General/[[UIAlertSheet alloc] initWithTitle:@"An Alert" buttons:buttons defaultButtonIndex:1 delegate:self context:self];
[alertSheet setBodyText:@"Do something?"];
[alertSheet popupAlertAnimated:YES];



<code>- (void)setTitle:(General/NSString*)title;</code>

<code>- (void)setBodyText:(General/NSString*)text;</code>

<code>- (void)addButtonWithTitle:(General/NSString*)title;</code>

Numbered, starting with one. Number is returned in delegate callback.

<code>- (void)setDelegate:(id)delegate;</code>

<code>- (void)presentSheetFromAboveView:(General/UIView*)view;</code>

<code>- (void)popupAlertAnimated:(BOOL)animated;</code>

<code>- (void)popupAlertAnimated:(BOOL)animated: atOffsett:(float)offset;</code>

<code>- (void)dismiss;</code>

Same as dismissAnimated:YES, I think.

<code>- (void)dismissAnimated:(BOOL)animated;</code>

**General/UIAlertSheet delegate methods**

<code>- (void)alertSheet:(General/UIAlertSheet*)sheet buttonClicked:(int)button;</code>

    
- (void)alertSheet:(General/UIAlertSheet*)sheet buttonClicked:(int)button
{
  if ( button == 1 )
    General/NSLog(@"Yes");
  else if ( button == 2 )
    General/NSLog(@"No");

  [sheet dismiss];
}
