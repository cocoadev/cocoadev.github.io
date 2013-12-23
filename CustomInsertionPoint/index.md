---
layout: page
title: CustomInsertionPoint
---



hello, i've been trying to implement a custom insertion point, sometimes refereed to as a caret, in a subclass of NSTextView. 

if i implement <code>- (void)drawInsertionPointInRect:(NSRect)aRect color:(NSColor *)aColor turnedOn:(BOOL)flag</code> in my subclass i can get the block caret i'm after. but in doing so i have to get the rect for the character at the insertion point in my <code>drawInsertionPointInRect:</code> and i ignore the rect given to the method.

now for the really problem. when inserting, typing, text into my sub-classed NSTextView i still get regular thin black line caret. my custom caret gets drawn after a blink of the caret.

i have found the <code>- (void)updateInsertionPointStateAndRestartTimer:(BOOL)flag</code> method which seems to be drawing the thin black caret. i have no idea what this method really needs to do. when i try and override it in my subclass of NSTextView i can only insert one character and i get no caret.

using the debugger i have found that the private method <code>_blinkCaret:</code> calls <code>drawInsertionPointInRect:</code>. now i have no idea what <code>_blinkCaret:</code> should be doing either.

if anyone can shed some light on this problem i would really appreciate it. if no one out there knows how to solve this problem but would like to help be solve it that would be great too. thank you all very much.

MattO

----
Believe it or not you can handle the first blink by overriding

<code>
- [NSTextView (void)_drawInsertionPointInRect:(NSRect)arg1 color:(NSColor *)color]
</code>

all other insertion point blinks appear to be handled by

<code>
- [NSTextView (void)drawInsertionPointInRect:(NSRect)rect color:(NSColor *)color turnedOn:(BOOL)flag]
</code>

I have no idea why... but it works on Leopard.

DaveMacLachlan

