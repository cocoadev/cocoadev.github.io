---
layout: page
title: HowToIntegrateIBPalette
---

Can anyone help me in integrating multiple controls into single Palette.

I have created many controls and I want to add these controls into a single palette.
----
If you're just working with views (including subclasses of NSControl or whatever), it's not that hard. Take a look at Apple's examples (specifically bMoviePalette, but also BusyPalette) for help. Basically you can just create an IBPalette project, open the nib file, and drag Custom Views onto the palette. Then set the class of each Custom View to be one of your controls. If the control is more specific (like a subclass of, say, NSButton), you can use that instead. The important part is that you change the class to be your control.

Finally, if this is just for your own personal use to save settings (not custom classes with their own inspectors or whatever), you can use Interface Builder's own palette creator. Choose New Palette from the Palettes submenu in the Tools menu, then hold option and drag your items out of a regular nib into the palette window. From then on you can use those items as templates. --JediKnil

----

Thanks for your suggestion.

But I have my own inspector for each of the control that I want to palettize. How to display the inspector based on the selected control?

I have implemented inspectorClassName method for each control in the palette to return the name of the inspector class name. But it is not working.
Is there any way to display customized inspector for each of the control?  

BusyPalette sample uses the default inspector. 

Is there any way to Palettize multiple controls into a single palette with each control having its own inspector. 

----
It's unrelated, actually. You have multiple controls in the palette, make sure each one implements     -inspectorClassName}, subclass IBInspector (somewhat nasty considering the lack of documentation), and remember to load the inspector's nib when you initialize it. For an example, you could take a look at RBSplitView, or for a really complicated non-view example, you can look at my GenericToolbar. If you haven't seen them, Sun also has some quite ancient IB docs at http://docs.sun.com/app/docs/doc/802-2110/ . Some of that is out of date, though, so make sure you check with a present-day palette. --JediKnil
    
@implementation MyControl (IBAdditions)
- (NSString *)inspectorClassName
{
    return @"MyInspector"
}
@end

@implementation MyInspector
- (id)init
{
    if (self = [super init])
        [NSBundle loadNibNamed:@"MyInspectorNib" owner:self];
    return self;
}

- (void)revert:(id)sender
{
    // stuff
}

- (void)ok:(id)sender
{
    // stuff
}
@end

