---
layout: page
title: MultipleToolbars
---



I'm trying to create a document window with one toolbar for each of its major modes, like XCode. 

I've approached the problem by creating (and retaining) two different toolbar instances. I implemented the basic delegate methods, providing a different set of allowed / default item identifiers depending on the toolbar. I swap the toolbars using     [window setToolbar:toolbar]; when the document's mode changes.

The problem is that, in certain circumstances, the following error occurs while swapping the toolbar (but not on the setToolbar: line, this is somewhere in the Cocoa frameworks):

    *** NSRunLoop ignoring exception **** -[NSCFArray addObject:]: attempt to insert nil' that raised during posting of delayed perform with target 550a0f0 and selector '_doDelayedValidateVisibleToolbarItems'

At this point, the toolbar that was switched to is missing items where it wasn't before. It's important to note (because I think this may be part of - if not all of - the problem): Both toolbars share some items - and the shared items are the ones that *stay* during the erroneous swap while the non-shared ones are the ones that disappear.

I'm thinking this is some problem with the auto-saving behavior. Looking the problem up here and on the cocoa-dev list archives, I found a reference to such a problem with a solution that worked by disabling the auto-save. The problem is, I **want** the user to be able to customize each toolbar with persistence.

Has anybody tackled this issue? How should this be handled?

**Note:** *I realize this is rarely good UI design, but I have a situation similar to XCode (multi-mode project window) and this solution really does work well -- I'm sure everyone has their opinions regarding UI design, but I'm looking for a solution to this problem, not a UI debate. Thanks! :-)*

----

I've never run into this problem, but maybe you could try retaining both toolbars and their items for the life of the program?

----

Thanks for the response. I use the following pattern:

    
- (NSToolbarItem *)toolbar:(NSToolbar *)toolbar
     itemForItemIdentifier:(NSString *)itemIdentifier
 willBeInsertedIntoToolbar:(BOOL)flag
{
    NSToolbarItem * toolbarItem = [[NSToolbarItem alloc] initWithItemIdentifier:itemIdentifier];
    [toolbarItem autorelease];

	if ([itemIdentifier isEqual:@"Inspector"]) {
        [toolbarItem setLabel:@"Inspector"];
        [toolbarItem setPaletteLabel:@"Document Inspector"];
        [toolbarItem setToolTip:@"Show the document inspector palette"];
        [toolbarItem setImage:[NSImage imageNamed:@"info"]];
        [toolbarItem setTarget:self];
	} else if ([itemIdentifier isEqual:@"New Item"]) {
...


Are you saying I should make each item a separate instance variable and instantiate them first? Should I also instantiate one Inspector item instance for each toolbar like     toolbarOneInspectorToolbarItem and     toolbarTwoInspectorToolbarItem?

----

Try making a single instance variable, a NSMutableArray called "items" or something. alloc and init it inside your init method, then add a quick

    
    [items addObject:toolbarItem];


to your above method. The array will retain the toolbar items, and release them when you release the array (in your dealloc method).

----
Thanks for the suggestion - could you explain it? :-) My first though, for example, is how is this different from creating the items the 'usual' way and giving them to the toolbar to retain / release when done?

----
The reply above is saying to add the toolbar item you create to an array (which is an ivar of the class which handles the toolbars). This will cause a small memory leak though; although since you're just testing at this point that doesn't matter. Here's another way to do it though...

    

static NSMutableDictionary *toolbarItems = nil;    // create this in +(void)initialize

- (NSToolbarItem *)toolbar:(NSToolbar *)toolbar itemForItemIdentifier:(NSString *)itemIdentifier willBeInsertedIntoToolbar:(BOOL)flag
{
    NSToolbarItem *item = [toolbarItems objectForKey: itemIdentifier];

    if ( item == nil )
    {
        item = [[NSToolbarItem alloc] initWithItemIdentifier:itemIdentifier];

        // setup the toolbar item here

        [toolbarItems setObject:item forKey:itemIdentifier];
        [item autorelease];
    }

    return item;
}



Make sure to create the toolbarItems dictionary, retain both toolbars as ivars, and let us know what happens!

*Made a minor adjustment -- is this what you meant?*

----

Okay - did that. I got the same error after switching between toolbars several times. I then told the method above to     return [item copy]; (ignoring the memory leak for now. That error went away, but now any toolbars not currently displayed when the window is resized produce drawing errors when you switch back to it after a resize. (sigh) What a pain!

A day later and still getting nowhere - I'm about to give up on this idea entirely; it just seems too friggin' buggy.

After even more attempts and frustration, I'm almost 100% certain there is a bug in the AppKit that's preventing this. Nothing in the documentation suggests this shouldn't work. The very structure of the whole NSToolbar system suggests it *should* work. Has **anybody** had any experience with this?

----

look whether you are calling [item autorelease] more than once. I had the same problem and found this solution, which works without anything.

*I don't have a solution to the specific problem, but you could use one toolbar and change the behavior of the *delegate*. That is, when the mode switches, change what's returned by     toolbarAllowedItemIdentifiers:,     toolbarDefaultItemIdentifiers:, and     toolbarSelectableItemIdentifiers:. To switch the items currently in the toolbar, use     configurationDictionary and     setConfigurationDictionary:. Warning: untested. --JediKnil*

