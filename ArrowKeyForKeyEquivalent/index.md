---
layout: page
title: ArrowKeyForKeyEquivalent
---

I had been trying to make Cocoa use an arrow key for a key command when building menus dynamically. (Using NSMenu) 
I figured out how and thought I'd share it here in case anyone else ever tries this.

I tried the old arrow key ASCII codes from OS 9. No Luck.
I also tried unicode arrows. No Luck.

So then I noticed that Preview.app uses Apple-arrow commands for Next Page and Previous Page. If I open up the MainMenu.nib file from Preview.app the key command for these menus appears as some Japanese character. (Left is ? and Right is ?)

So then I promoted my source file to UTF-8 and tried specifying these Japanese characters in a normal @"?" style.  

    
 [ menu addItemWithTitle:@"Previous" action:@selector(prev:) keyEquivalent:@"?"];


Still no luck.

What finally worked was using: 

    
 [ menu addItemWithTitle:@"Previous" action:@selector(prev:) keyEquivalent:[NSString stringWithUTF8String:"?"]];


So I guess the morals of the story are: 
- If you want to do something weird, see how Apple does it.
- The @"" shortcut doesn't work correctly with UTF-8.

- KenAspeslagh

Hmm, the Japanese characters just look like a ? in my post. If you need the actual character, just look in Preview.app like I did.

----

In the NSEvent.h header, there's a list of Unicode characters reserved for function keys, two of which are NSLeftArrowFunctionKey and NSRightArrowFunctionKey. It's odd that those are not the values used for this purpose.  -- Bo

----

The UTF-8 characters I was using for left and right are: 0xef9c82 and 0xef9c83.

However, the constants Bo pointed out seem to work as well:

    
 [ menu addItemWithTitle:@"Previous" action:@selector(prev:) keyEquivalent:[NSString stringWithFormat:@"%C", NSRightArrowFunctionKey]];


I'm going to use those instead! THANKS!

-Ken

