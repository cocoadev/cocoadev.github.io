---
layout: page
title: ToolbarSearchItem
---



I am creating a program that uses a toolbar. I would like to put a search item into it but have encountered problems. I have tried many ways : Use a General/NSView with a General/NSSearchField inside it; then creating a custom General/NSToolbarItem. Use General/GenericToolbar and try to find and modify the code to insert a custom item. Look at Xcode examples and then piece together information enough to create it all by myself. None of these have worked and the process for doing it is hard. In pre-reply: Yes, looked at the cocoa docs in Xcode. Yes, I have been to Cocoa Dev Central and in the archives. Yes, I have been General/MacDevCenter. I would like someone to shed some light on the subject if htey have been successful where I have failed.
 
Thanks, 
William Whitlock

----

1 - You misspelled the page title. Please take care with that as it can't be changed (the content must be moved to a new, correctly-spelled page and this one tagged for deletion).

2 - You've failed to state what you've done or list the code you've used, or even the errors / problems you're receiving. Do you want clarification, or a complete, gift-wrapped solution?

3 - Since what you're asking for is a common feature, it's unlikely you wouldn't have found the many threads/discussions/pages on this very topic had you searched thoroughly. Searching the cocoa-dev archives (at cocoabuilder.com), for example, for "toolbar search" produces results on the very first page. Likewise with Google and "General/NSToolbar General/NSSearchField".

4 - Creating a page in General/MailingListMode and failing to General/PostYourCode is bad enough, but cutting off potential responses by saying, "yes, I've done this, yes, I've done that" is likely to put most people off due to the arrogance. It's like saying, "I don't want to be pointed in the right direction, I just want you to tell me exactly what to do." This kind of attitude won't help you find answers because it suggests you don't want to learn, just handed someone else's hard work.

With that said, see number 3 above ... try the search terms I mentioned. You'll find your answer a million times over with commentary and even some pictures.

----

I think when putting a view into a toolbar, you need to set the minimum and maximum sizes, or else it doesn't work. This is what I have when I do this:

    

-(General/NSToolbarItem *)toolbar:(General/NSToolbar *)toolbar itemForItemIdentifier:(General/NSString *)itemIdentifier willBeInsertedIntoToolbar:(BOOL)flag {
  General/NSToolbarItem* toolbarItem = General/[[[NSToolbarItem alloc] initWithItemIdentifier:itemIdentifier] autorelease];
  if([itemIdentifier isEqual:General/ToolbarSearch]) {
    [toolbarItem setLabel:@"Search"];
    [toolbarItem setTarget:self];
    [toolbarItem setView:[self searchView]];
    [toolbarItem setMinSize:General/NSMakeSize(32.0,32.0)];
    [toolbarItem setMaxSize:General/NSMakeSize(135.0,135.0)];
    [toolbarItem setAction: @selector(doToolbarSearch:)];        
    return toolbarItem;
  }
  return nil;
}


- General/DavidThorpe

----

Sorry, about the bad begining; But, I am very new so if you have any tips you can post it to my user file on http://cocoadev.com/index.pl?General/WilliamWhitlock . (I mean tips besides the ones listed above by the way).

I was able to get it to work after many tries. I used ADC's Search Item code (David's works also) and connected an General/NSView with a search item to an outlet. Then, I went to my Contoller.h file and added a line of code like that in O'Reilly's guide for a General/NSToolbar: http://www.macdevcenter.com/pub/a/mac/2002/03/15/cocoa.html .

    

#define General/SearchDocToolbarItemIdentifier @"General/SearchDocToolbarItemIdentifier"



So now it works perfectly with the required 'Allowed and General/DefaultNSToolbarItems' implemented. Thanks for all the help!

P.S. The ADC helper leaves out that key bit of info and I wasn't able to find it any where else.

Again, Thanks
William Whitlock
