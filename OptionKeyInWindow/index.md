---
layout: page
title: OptionKeyInWindow
---

I am trying to detect when the option key is being held down in my window (I'm ultimately trying to do something like iTunes, where you can hold option to have the add playlist button change into a gear icon for adding smart playlists).  I have the following code, but it does not work -- it always says the option key is being held down, even when I have released it.  The following is part of my NSWindow subclass.

    - (void)flagsChanged:(NSEvent *)theEvent
{
	[super flagsChanged:theEvent];

	if ([theEvent modifierFlags] && NSAlternateKeyMask)
		NSLog(@"Option down");
	else
		NSLog(@"Option up");
}

----

&& is the C boolean AND operator. You want the bitwise AND operator, &.

----

Thanks, what exactly is the bitwise AND operator?

*
http://www.google.com/search?q=%22bitwise+and+operator%22
*

----

By using the boolean AND operator, && (two ampersands), you're writing the equivalent of this:

    
if( ([theEvent modifierFlags] != 0) && (NSAlternateKeyMask != 0) )


NSAlternateKeyMask is always non-zero. The modifierFlags are as well, since there are probably some private flags that are always set.

Using the bitwise AND searches for matching bits. For example, if you bitwise AND these two numbers:

    
| 01010111001
| 10011001101
| ----------- bitwise AND
| 00010001001


Where the 1's match you get 1, otherwise you get zero. NSAlternateKeyMask looks like this:

    
...000000100000...


So by doing a bitwise AND you check to see whether that single bit (flag) is set.

