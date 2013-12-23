---
layout: page
title: NSMagnifier
---



In some applications, it is desirable to be able to choose a color from somewhere on the visible screen. This can be done using the standard NSColorPanel attached to an NSColorWell, but this can sometimes be disruptive to flow when the goal is decidedly to pick a color from on-screen (as opposed to choosing one from elsewhere in the color panel).

Acorn (http://flyingmeat.com/acorn/) provides a direct color pick-up tool (much like in Photoshop and other apps), but it uses OS X's native capabilities. A process sample reveals an undocumented Apple class: _NSMagnifier.

In order to use _NSMagnifier, you first need to stick a portion of its interface into one of your header files so the compiler won't whine.

    
@interface _NSMagnifier : NSObject {}
+ (id)sharedMagnifier;
- (void)trackMagnifierForPanel:(id)fp8;  // I'm not sure if fp8 is significant, but I left it
@end


Alternatively, if you're OK with "may not respond to selector" compiler warnings, you can simply put a forward declaration for the class in one of your headers (you do not need to do both!):

    
@class _NSMagnifier;


Once you've got that, you can grab magnifier focus from any method. In this case, an IBAction might make sense:

    
- (IBAction)grabColorFromScreen:(id)sender
{
  _NSMagnifier *mag = [_NSMagnifier sharedMagnifier];
  [mag trackMagnifierForPanel:[NSColorPanel sharedColorPanel]];
}


You can then process things via the standard routes for NSColorPanel. For instance, you can set a delegate for the shared color panel, and then respond to the -changeColor: method:

    
- (void)awakeFromNib
{
  // ...
  [[NSColorPanel sharedColorPanel] setDelegate:self];
}

- (void)changeColor:(id)sender
{
   [myColorWell setColor:[sender color]];
}

