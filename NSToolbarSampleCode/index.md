---
layout: page
title: NSToolbarSampleCode
---

There's a decent bit of starter code here:

http://cocoadevcentral.com/articles/000037.php#000037


Any others?

 - Yes - in fact the code in the above article is a bit misleading as to how toolbars work.  A much better article is here:

http://macdevcenter.com/pub/a/mac/2002/03/15/cocoa.html

Careful with this sample code though, there is a small error:

    
- (void)setupToolbar
{
    NSToolbar *toolbar = [[NSToolbar alloc] initWithIdentifier:@"mainToolbar"];
    [toolbar autorelease]; // wrong
    [toolbar setDelegate:self];
    [toolbar setAllowsUserCustomization:YES];
    [toolbar setAutosavesConfiguration:YES];
    [mainWindow setToolbar:[toolbar autorelease]];
}



As you can see, there is a double autorelease, which is incorrect; it will cause the program to crash.  The first autorelease call should be removed.

--Edit February 19,2003

----

An efficient method to validate toolbar items based on multiple conditions. This needs to go in your toolbar's **target** not your toolbar controller

    
- (BOOL)validateToolbarItem:(NSToolbarItem *)theItem
{
    NSMutableSet *itemsToDisable = [NSMutableSet setWithCapacity:0];
    
    if (someCondition)
      {
	NSSet *myItems = [NSSet setWithObjects:@"toolbarItemIdentifier1", @"toolbarItemIdentifier2", 
                         @"toolbarItemIdentifier3", NULL];
	[itemsToDisable unionSet:myItems];
      }

    if (!anotherCondition)
      {
	NSSet *myItems = [NSSet setWithObjects: @"toolbarItemIdentifier4", NULL];
	[itemsToDisable unionSet:myItems];
      }
    return ![itemsToDisable containsObject:[theItem itemIdentifier]];
}

----
Ok, I have MainToolbar.h and MainToolbar.m, and then I have MainDocument.h and MainDocument.m. I want to know how to create an action for MainDocument that will change the the selected NSToolbarItem. How would I go about doing this?

----

Add an action to FirstResponder in MainDocument's nib, and implement the action in MainToolbar to call     setSelectedItemIdentifier:

----

If you want to create a toolbar in Interface Builder 3 or higher, the instructions at http://www.katoemba.net/makesnosenseatall/tag/nstoolbar/ are of great help.

In a nutshell, you need to add NSToolbar from the Library tools to the window of your choice, add NSToolbarItems as you see fit, and link the toolbar item to the function you want it to call.
You still need to implement the following function to make the buttons clickable:
    
- (BOOL)validateToolbarItem:(NSToolbarItem *)theItem
{
    if ([theItem tag] == 1001) // Save toolbar item
        return [self isDocumentEdited];
    return YES;
}

 Pour devenir figurant sur   maintiennent numéro, vous aurez  votre compte   Agent  (code RIO ) [http://obtenir-rio.info numéro rio]. Vous obtiendrez  pouvez obtenir  pour  gratuit  par appelant   mots  du serveur ou du service à la clientèle  satisfaction client  votre propre  vieille fournisseur  [http://obtenir-rio.info/rio-bouygues rio bouygues] . Vous ne  CAN   get un SMS  avec vos . Avec  votre propre  [http://obtenir-rio.info/rio-orange code rio orange], alors  vous serez en mesure de vous abonner à l' offre de  de   en  rouge.

