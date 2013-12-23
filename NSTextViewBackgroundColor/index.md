---
layout: page
title: NSTextViewBackgroundColor
---

How do i set the background color of a NSTextView using Panther's     changeDocumentBackgroundColor method without the text view's text changing color to match?

I have a NSColorWell with its target set to that method (which I added to my nib's FirstResponder, since I couldn't find it in the text view) but when I use it, the text color changes along with the background color - NOT helpful!

I was using this method:

    
-(IBAction)setBackgroundColor:(id)sender
{
    [window makeFirstResponder:nil];
    [textView setBackgroundColor:[sender color]];
    [textView setNeedsDisplay:YES];
}


which worked if there was no selection in the text view, but if there was, it would change the text color and leave the bg color alone.

I replaced the     [textView setBackgroundColor:[sender color]]; above with     [textView changeDocumentBackgroundColor:sender], and with this, the text color is first changed to the initial color of my color well (white), then the background color change occurs normally as i change values in the NSColorPanel.

I'm sure this is really simple to do, but I just can't figure it out... AppKit's not helping me here!

----

When you say *text color*, I assume you mean the text background color, as opposed to the document background color or text foreground color (which is usually referred to as text color).

I think the text background color is initially undefined, or transparent. If you just set the text background color explicitly, it should work. The text background color is an attribute of the attributed string.

�DustinVoss

----

I mean the document background color. I'm trying to set it from a color well in my window instead of the font panel. I've gotten it to stop changing the text foreground color of the selection by unchecking 'Allows user to change fonts' in IB, but I'm still getting color mixups between the wells. Sometimes a freakin' modal dialog would make life so much easier... (Not that I want to go back to modal dialogs!) Here's my relevant code now:

    
-(void)awakeFromNib
{
    [textView setAllowsDocumentBackgroundColorChange:YES];
    [[NSColorPanel sharedColorPanel] setTarget: self];
    [[NSColorPanel sharedColorPanel] setAction:@selector(changeTextViewColor:)];
}

-(IBAction)changeTextViewColor:(id)sender
{
    [window makeFirstResponder: nil];
    
    if ([sender isEqual: bgColorWell])
    {
	[self setBackgroundColor:[sender color]];
    }
    else if ([sender isEqual: fgColorWell])
    {
	[self setForegroundColor:[sender color]];
    }
}

-(void)setBackgroundColor:(NSColor *)aColor
{
    [textView setBackgroundColor:aColor];
    [textView setNeedsDisplay: YES];
}

-(void)setForegroundColor:(NSColor *)aColor
{
    textView textStorage] setForegroundColor: aColor];
    [textView setNeedsDisplay: YES];
}


When I use the background color well with this, if my text view is first responder, it first changes the text foreground color to white (the initial color I have set up for the background color well in IB). If the text view isn't first responder, it works OK.

----

Well, I see one problem with the code. When checking which control the sender is, use ==, not isEqual:. == is used to see if an object is identical to another. *Identical* is a stronger relationship than *equal*.

According to file:///Developer/Documentation/Cocoa/Conceptual/[[DrawColor/Tasks/ChoosingColors.html, when you click Apply in the color panel, it sends two messages: a -changeColor: to the first responder, and the target/action message. It sounds like the -changeColor: message to the text view is taking effect.

I don't see an easy way around this. Maybe you can sub-class NSTextView to not respond to -changeColor:, or work with the color wells' target/action instead of the color panel's. Or you can write your own color panel **:)**. I guess the first step is to verify that this is what is actually going on.

----

NSTextView doesn't normally draw a background color. The white you see as the background of a text view is actually being drawn by the enclosing NSScrollView. If you just want to change that color, you should be asking the scroll view to change its background color, not the text view.

----

I too needed to do this recently, I've posted the code on my blog at http://cocoalatte.info/2007/04/20/text-view-color-syncing/.

Enjoy, AlanRSmith

