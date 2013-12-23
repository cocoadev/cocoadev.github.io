---
layout: page
title: PopupSliders
---




I'm looking for sample code to create a popup slider control, like the opacity controls used in recent versions of PhotoShop. Can anybody help?

To clarify:

I want to have an input box for numeric values, with a clickable triangle on its right, much like a combo box. When you click on the triangle, a slider appears below the input box, and you can drag the knob on the slider to adjust the value in the input box.

I will eventually insert popup sliders into an NSTableView, but I have to figure out how to use NSTableViews with different types of controls for each column first. (checkboxes, popup buttons, color wells, NSTextFields, NSTextFields with popup sliders, etc.

Sample code for NSTableViews with different control types for each column would also be helpful.


Duncan C

----
The only way I can think of to do this well is by using a borderless window for the slider control that appears when you click the button and disappears after you click outside. NSBorderlessWindowMask is what you would want for this, just look in the NSWindow docs. As for using it in a table view, you would have to make the original entry field not only with NSControl but also using NSCell, therefore you can then use it in an NSTableView, and then have the window appear from there when the cell is clicked. Hope this helps. --LoganCollins
----
As sometimes happens, I thought this was an interesting question so in the hour or two since I noticed the question, I have built a working example complete with an xCode project: ** http://www.stepwise.com/EMB/PopupHelper/PopupHelper.zip **


Here is how it works:
1) In Interface Builder, place a small button next to whatever NSControl you want to give a pop-up helper.  For example, if you have an NSTextField in IB, place a small button next to it.

2) Create an instance of PopupHelper class (source code below) in the same nib as the  NSControl you want to give a pop-up helper.

3) Connect the PopupHelper instance's controlToHelp outlet to the control that needs help.

4) Connect the target/action of the button to the PopupHelper instance's -toggleShowHelper: action.

5) Create a new empty nib.  Drag an Custom View into the instances area of the empty nib.  Then place whatever controls you want in the custom view.  Sliders and button work well in the custom view, but text views do not because they want to be in a key window and popup helper windows never become key.

6) Make the File's Owner of the new nib be of class PopupHelper.

7) Bind the values of the controls in the custom view to whatever you want using the File's Owner->controlToHelp key path.  For example, bind a sliders value to File's Owner->controlToHelp.floatValue

8) Save the new nib in your project.

9) set the title of the button next to the control to help to the name of the nib you just saved.  This is a hack, but it works well if the button is set to display icon only.  The title will never be seen by a user.  The title of the button is just a convenient way to identify which nib to load when it is necessary to display the popup helper.

10) Build and run.  When the button next to the control to help is pressed, the nib containing helper controls is loaded and the controls are added to a child window positioned next to the control that needs help.

There are several subtleties: 

A) The control to help is made into the firstResponder when the popup is displayed.  

B) The value binding only works one way unless you properly use Key Value Observing, but it is seldom an issue and is mitigated if you bind the helper controls to model values instead of the control to help.

C) Moving the window containing the control to help works fine, but resizing the window can cause temporary misalignment of the helper popup and the control to help.  The easiest way to solve the problem is to just hide all popups when the window is resized.  See + (void)hideAllPopupHelpers;

D) Because of the the dynamic nib loading approach, you can have complex popup helpers and different popup helpers for every control if you want...

    
#import <Cocoa/Cocoa.h>


@interface PopupHelper : NSObject <NSCoding>
{
   IBOutlet NSControl   *controlToHelp;  // determines position of helper window
   IBOutlet NSView      *helperView;     // set when nib containing helper controls is loaded
   NSWindow             *popupWindow;    // child window created on demand to hold helper controls
}

+ (void)hideAllPopupHelpers;

- (IBAction)togleShowHelper:(id)sender;

- (NSControl *)controlToHelp;
- (void)setControlToHelp:(NSControl *)aControlToHelp;
- (NSView *)helperView;
- (void)setHelperView:(NSView *)aHelperView;

@end


    
#import "PopupHelper.h"


@implementation PopupHelper

// Class varaible is used to enable hiding of all helper popups
static NSMutableSet     *existingHelpers = nil;


- (void)dealloc
{
   [[NSNotificationCenter defaultCenter] removeObserver:self];
   [self setControlToHelp: nil];
   [self setHelperView: nil];
   [popupWindow release];
   [super dealloc];
   popupWindow = nil;
}


- (void)encodeWithCoder:(NSCoder *)coder 
{
   [coder encodeObject:[self controlToHelp] forKey:@"controlToHelp"];
}


- (id)initWithCoder:(NSCoder *)coder 
{
   [self setControlToHelp:[coder decodeObjectForKey:@"controlToHelp"]];
   return self;
}


+ (void)hideAllPopupHelpers
{
   [existingHelpers makeObjectsPerformSelector:@selector(hideHelper)];
   [existingHelpers release];
   existingHelpers = nil;
}


+ (void)unregisterVisiblePopupHelper:(id)aHelper
{
   if([existingHelpers containsObject:aHelper])
   {
      [existingHelpers removeObject:aHelper];
   }
}


+ (void)registerVisiblePopupHelper:(id)aHelper
{
   if(nil == existingHelpers)
   {
      existingHelpers = [[NSMutableSet alloc] init];
   }
   
   [existingHelpers addObject:aHelper];
}


- (void)controlWindowDidUpdate:(NSNotification *)aNotification
{  // Reposition the popup helper after the controlToHelp's window updates
   NSPoint position = NSMakePoint(NSMaxX(self controlToHelp] frame]), [[NSMinY(self controlToHelp] frame]));
   position = [[[self controlToHelp] superview] convertPoint:position toView:nil];
   position = [[[self controlToHelp] window] convertBaseToScreen:position];
   [popupWindow setFrameTopLeftPoint:position];
}


- (void)showHelper:(id)sender
{
   if(nil == [self helperView])
   {
      [[[NSBundle loadNibNamed:[sender title] owner:self];
   }
   
   if(nil != [self helperView])
   {
      NSRect contentRect = [NSWindow frameRectForContentRect:self helperView] frame] styleMask:[[NSBorderlessWindowMask];         
      NSPoint position = NSMakePoint(NSMaxX(self controlToHelp] frame]), [[NSMinY(self controlToHelp] frame]));
      
      [[self class] hideAllPopupHelpers];
      [[[self controlToHelp] window] makeFirstResponder:[self controlToHelp;
      popupWindow = [[NSWindow alloc] initWithContentRect:contentRect styleMask:NSBorderlessWindowMask backing:NSBackingStoreBuffered defer:NO];
      [popupWindow setReleasedWhenClosed:NO];
      [popupWindow setContentView:[self helperView]];
      position = [self controlToHelp] superview] convertPoint:position toView:nil];
      position = [[[self controlToHelp] window] convertBaseToScreen:position];
      [popupWindow setFrameTopLeftPoint:position];
      [[[self controlToHelp] window] addChildWindow:popupWindow ordered:[[NSWindowAbove];
      [popupWindow orderFront:self];
      self class] registerVisiblePopupHelper:self];    
      
      [[[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(controlWindowDidUpdate:) name:NSWindowDidUpdateNotification object:self controlToHelp] window;  
   }
}


- (void)hideHelper
{
   [self controlToHelp] window] removeChildWindow:popupWindow];
   [popupWindow orderOut:self];
   [popupWindow close];
   [popupWindow release];
   popupWindow = nil;
   
   [[[[NSNotificationCenter defaultCenter] removeObserver:self name:NSWindowDidUpdateNotification object:self controlToHelp] window;  
}


- (IBAction)togleShowHelper:(id)sender
{
   if(nil == popupWindow && nil != [sender title] && nil != [self controlToHelp])
   {
      [self showHelper:sender];
   }
   else
   {
      [self hideHelper];
      [self setHelperView:nil];
   }
}


- (NSControl *)controlToHelp 
{
   return controlToHelp; 
}

- (void)setControlToHelp:(NSControl *)aControlToHelp 
{
   [aControlToHelp retain];
   [controlToHelp release];
   controlToHelp = aControlToHelp;
}

- (NSView *)helperView
{
   return helperView; 
}

- (void)setHelperView:(NSView *)aHelperView
{
   self class] unregisterVisiblePopupHelper:helperView];
   [aHelperView retain];
   [helperView release];
   helperView = aHelperView;
}

@end


----

An alternative, and in my opinion a much easier class to do this is [[GCWindowMenu, available here: http://apptree.net/gcwindowmenu.htm One line creates the menu with any view you care to give it - for example an NSSlider from a NIB, as demonstrated in the example project, and one more line does the rest - displaying the menu, tracking it, and fading it away quickly at the end. --GrahamCox

----
It is nice to see alternative approaches.  The gcwindowmenu has some polish that the other example lacks.  However, to be fair, the first example doesn't require any code to use the existing classes.  The gcwindowmenu requires "two lines" of code (as if that matters).
Both examples require the existance of a view to be displayed in a window etc.  The two examples are very similar.

----

Fair comment - I suppose what you consider easier is down to your perspective. For me, it seems extraordinary what lengths some folk will go to to avoid writing a teeny bit of code to glue a few bits of UI together, but I accept that there are those that prefer any amount of wiring in a NIB to that. The popularity of bindings proves I'm in the minority there ;-) (and yes, I realise I'm grossly oversimplifying what bindings are about). I guess I can reduce my solution to no code at all by making a popup button subclass that you can put in a NIB and then wire its 'popUpView' outlet to some other view such as a slider. That sounds like it could be worth doing, though there are plenty of times you might want a menu that isn't associated with a button (such as a special contextual menu for example). Inevitably code that implements this sort of functionality will at heart be similar - after all there's really only one way to make a pop-up window - where I think it differs considerably is the amount of work involved in actually making it work - a couple of lines of simple code vs. 9 complicated steps hacking around in the NIB (and yes, the naming hack is a hack). Still, I've no axe to grind - I wrote this primarily for my own benefit; if it works for you that's good - if you prefer the other solution, that's good too! --GC

----
Fair comment ;-)  Both examples need steps 1,3,4,7, and 10 identified in the first example.  If the pop-up view is stored in a separate nib with the gcwindowmenu example, both examples also need steps 5, 6, and 8.  So, in some cases, the gcwindowmenu approach uses two lines of code in order to avoid steps


2) Create an instance of PopupHelper class in the same nib as the  NSControl you want to give a pop-up helper.


and


9) set the title of the button next to the control to help to the name of the nib you just saved.  This is a hack, but it works well if the button is set to display icon only.  The title will never be seen by a user.  The title of the button is just a convenient way to identify which nib to load when it is necessary to display the popup helper.

I agree that step 9 is a horrible hack, and avoiding it more than justifies the use of two lines of code.  In fact, I would probably use the gcwindowmenu in preference to the first example.  There is an appeal to a "no code" solution though - EMB.

