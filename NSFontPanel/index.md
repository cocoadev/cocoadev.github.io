---
layout: page
title: NSFontPanel
---



Most aspects of NSFontPanel are described in Apple's documentation on:

http://developer.apple.com/documentation/Cocoa/Conceptual/FontPanel/index.html

specifically you need to implement the method     changeFont: along the lines of:
    
� (void)changeFont:(id)sender 
{
    NSFont *oldFont = [self font]; 
    NSFont *newFont = [sender convertFont:oldFont]; 
    [self setFont:newFont]; 
    return; 
}


What is not described is that you might want to implement     changeAttributes: along the lines of:
    
� (void)changeAttributes:(id)sender 
{
    NSDictionary *oldAttributes = [self fontAttributes];
    NSDictionary *newAttributes = [sender convertAttributes: oldAttributes];
    [self setFontAttributes:newAttributes];
    return; 
}


to allow the font panel to set the underline, strikethrough and shadow attributes.

For setting the background color from the font panel, try this: (sender is an NSColorPanel)
    
-(void)changeDocumentBackgroundColor:(id)sender
{
    [self setBackgroundColor:[sender color]];
}


----

NSFontPanel has a method **_showCharacterPalette:**. I don't know what type the parameter is; it is probably an id. *Most likely (id)sender.*

----

How can I tell the NSFontPanel to set the Shadow to on or off, as well as other such attributes?

The reason I need this is because when my program loads, it reads in the settings from the prefs and it turns on the shadows (for example), but NSFontPanel, when brought up, still says they are off... and clicking them on, instead turns them off in the program's view... its really annoying and confusing for the user.

----

The solution:

[[NSFontManager sharedFontManager] setSelectedAttributes:yourAttributes isMultiple:NO];

----

the above is not working for me. i call the set method before bring up the panel and the settings from my preferences aren't reflected in the panel. what am i missing. thanks. 

and along these lines how do i set the background color in the font panel? i can get the value that the user changes, but i can't figure out how to set the color in the font panel. thanks again.


matt

----

unless i get the attributes and background color setting to come up in the font panel i'm looking into the accessory view for the font panel. i haven't been able to find any documentation. how do i get setting to and from the accessory view? thanks.


matt

