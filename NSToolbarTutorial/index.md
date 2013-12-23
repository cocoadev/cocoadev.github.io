---
layout: page
title: NSToolbarTutorial
---



This is a set of source code that concentrates on NSToolbar, and NSToolbarItem

As people have been requesting it, here is some stripped source of a complete NSToolbar implementation from one of my apps. It works, it just may not be the best method to do it. It's my interpretation of the documentation basically, and yes, I use awakeFromNib usually (although I would recommend creating the toolbar when the window has finished loading). -- MatPeterson
    

@interface ToolbarClass : NSObject
{
    IBOutlet id window; // Window to link toolbar to
    NSToolbarItem *import; // Item that we are adding
}
- (void)setupToolbar;
@end

@implementation ToolbarClass

- (void)awakeFromNib
{
    // Item created
    import = [[NSToolbarItem alloc] initWithItemIdentifier:@"Import"];
    [import setLabel:NSLocalizedString(@"Import", nil)];
    [import setToolTip:@"Import just the selected songs based on the preferences that you have set"];
    [import setPaletteLabel:[import label]];
    [import setImage:[NSImage imageNamed:@"import"]];
    [import setTarget:self];
    [import setAction:@selector(import:)];

    [self setupToolbar];
}

- (NSToolbarItem *)toolbar:(NSToolbar *)toolbar
itemForItemIdentifier:(NSString *)itemIdentifier
willBeInsertedIntoToolbar:(BOOL)flag
{
    NSToolbarItem *item = [[NSToolbarItem alloc] initWithItemIdentifier:itemIdentifier];

    if ([itemIdentifier isEqualToString:@"Import"])
    {
        return import;
    }
    return [item autorelease];
}

- (NSArray *)toolbarAllowedItemIdentifiers:(NSToolbar*)toolbar
{
    return [NSArray arrayWithObjects:NSToolbarSeparatorItemIdentifier,
        NSToolbarSpaceItemIdentifier,
        NSToolbarFlexibleSpaceItemIdentifier,
        NSToolbarCustomizeToolbarItemIdentifier, @"Import", nil];
}

- (NSArray *)toolbarDefaultItemIdentifiers:(NSToolbar*)toolbar
{
    return [NSArray arrayWithObjects:NSToolbarSeparatorItemIdentifier, @"Import", nil];
}

- (void)setupToolbar
{
    toolbar = [[NSToolbar alloc] initWithIdentifier:@"mainToolbar"];
    [toolbar autorelease];
    [toolbar setDelegate:self];
    [toolbar setAllowsUserCustomization:YES];
    [toolbar setAutosavesConfiguration:YES];
    [window setToolbar:toolbar];
}

- (IBAction)import:(id)sender
{
// Do something here that needs to be done when the toolbaritem is clicked.
}

@end



Or you could try the more traditional delegate category on your window controller, and implement the setupToolbar method in the latter.

    
#import "MyToolbarDelegateCategory.h"

@implementation MyWindowController ( MyToolbarDelegateCategory )

- ( NSArray * ) toolbarAllowedItemIdentifiers: ( NSToolbar * ) toolbar
{
	return [ NSArray arrayWithObjects:  NSToolbarSeparatorItemIdentifier,
			NSToolbarSpaceItemIdentifier,
			NSToolbarFlexibleSpaceItemIdentifier,
			NSToolbarCustomizeToolbarItemIdentifier, 
			@"FooItem", @"BarItem" @"BazItem", nil ];
}

- ( NSArray * ) toolbarDefaultItemIdentifiers: ( NSToolbar * ) toolbar
{
	return [ NSArray arrayWithObjects:  @"FooItem",@"BarItem", @"BazItem",
			NSToolbarFlexibleSpaceItemIdentifier,
			NSToolbarCustomizeToolbarItemIdentifier, nil ];
}

- ( NSToolbarItem * ) toolbar: ( NSToolbar * ) toolbar
	itemForItemIdentifier: ( NSString * ) itemIdentifier
       willBeInsertedIntoToolbar: ( BOOL ) flag
{
	NSToolbarItem *item = [ [ NSToolbarItem alloc ] initWithItemIdentifier: itemIdentifier ];
	
	if ( [ itemIdentifier isEqualToString: @"FooItem" ] )
	{
		[ item setLabel: NSLocalizedString( @"Foo", nil ) ];        // Easy to localize!
		[ item setPaletteLabel: [ item label ] ];
		[ item setImage: [ NSImage imageNamed: @"foo.png" ] ];
		[ item setTarget: myArrayController ];              // or setTarget: self
		[ item setAction: @selector( fooAction: ) ];
    }
	else if ( [ itemIdentifier isEqualToString: @"BarItem" ] )
	{
		[ item setLabel: NSLocalizedString( @"Bar", nil ) ];
		[ item setPaletteLabel: [ item label ] ];
		[ item setImage: [ NSImage imageNamed: @"bar.png" ] ];
		[ item setTarget: myArrayController ];
		[ item setAction: @selector( barAction: ) ];
    }
	else if ( [ itemIdentifier isEqualToString: @"BazItem" ] )
	{
		[ item setLabel: NSLocalizedString( @"Baz", nil ) ];
		[ item setPaletteLabel: [ item label ] ];
		[ item setImage: [ NSImage imageNamed: @"baz.png" ] ];
		[ item setTarget: self ];
		[ item setAction: @selector( bazAction: ) ];
    }
	
    return [ item autorelease ];
}

@end



see also GenericToolbar

