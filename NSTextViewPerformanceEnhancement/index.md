---
layout: page
title: NSTextViewPerformanceEnhancement
---

More discussion at FastWritesToNSTextView

I'd like to start an open discussion on how to squeeze larger files (text, rtf, rtfd) into an NSTextView. Scrolling is reasonable in huge plain text files, but dog-slow when you edit the file near the beginning (eg, insert or delete text). Is this a fundamental limitation or can some extra code make a more efficient editor that edits only part of a file at a time? 

----

My guess would be that NSTextStorage is not smart, and has to copy the entire contents of the buffer every time you add something at the beginning. A custom subclass that uses a gap buffer would probably help. However, it would be good to study the thing in Shark before taking any action.

*Anyone care to elaborate on this?*

----

Imagine you are writing a mutable string class. The obvious way to store the data would be something like this:
    
unichar *buffer;
unsigned bufferLength; // how many characters does it have now
unsigned bufferSize; // how many characters can it hold

Now you want to insert a character. What's the code look like?
    
- (void)insertCharacter:(unichar)c atIndex:(unsigned)i {
   if(bufferLength == bufferSize) {
      bufferSize *= 2;
      buffer = realloc(buffer, bufferSize);
   }
   memmove(&buffer[i+1], &buffer[i], sizeof(*buffer) * (bufferLength - i));
   buffer[i] = c;
   bufferLength++;
}

There are many variations on this theme depending on how you manage the memory, but they all have one important thing in common; in onder to insert a character in the middle of the string, all of the following characters have to be moved one space over to make room. That's what the call to     memmove() is doing here. If you have a very large string and are inserting characters near the beginning, you have to copy almost the entire string every time a new character is inserted, and this can become time-consuming.
A "gap buffer" is much like the code above, except that there is a "gap" kept in the middle of the buffer. The gap is just a small space in the middle of the buffer that has no data in it. If you try to insert a character and the location is right before the gap, then nothing has to be moved around. Otherwise, the gap is moved to the current location or is expanded if it's shrunk to nothing. Both of these require moving lots of memory around, but it happens much less often because consecutive insertions (like what happens when you type) only rarely require copying the string around, instead of requiring it every time.

The gap buffer is a pretty standard technique for heavy-duty text editors, and I'd guess that NSTextStorage doesn't make use of it.

----

So to minimize the amount of work needed to create such a system, could I then override the appropriate NSMutableString/NSMutableAttributedString and NSTextStorage methods to use a gap buffer, then override the appropriate NSTextView methods to inform the NSTextStorage of the cursor position (to position the gap when the location has drastically changed)?

----

Since these are all ClassClusters, you'd have to just implement your own version of NSTextStorage. The string part should be fairly straightforward, but storing the attributes is probably trickier.

Note that there's no need to follow the insertion point around. You don't move the gap when the insertion point moves (that would be unnecessary and inefficient), but only when characters are actually inserted and the gap is in the wrong place.

Before you even think about doing any of this, you should really code a test case that shows the slow behavior and then profile the whole thing in Shark so that you can see what's actually slow instead of just theorizing about it.

----

Those cautions about sharking the problem are wise.  I'm having some trouble interpreting the report, but the problem may lie with layout more than with storage.

----

Okay, a little bit more info.  

First, this is a good thread. < http://www.cocoabuilder.com/archive/message/cocoa/2002/11/12/7278 >.  Douglas Davidson is an Apple employee.

Second, it matters what the characteristics of your body of text are.  In my first test, I used a huge sequence of characters with no line breaks.  There it looks like the bottle neck is figuring out the total lines of text (has to be known to draw the scrollbar).   In a text file with line breaks at reasonable intervals, everything is MUCH faster, and the bottlenecks are     -[NSLayoutManager glyphRangeForCharacterRange:actualCharacterRange:] and     -[NSLayoutManager deleteGlyphsInRange:].

Both of those methods spending most of their time in     _replaceElements, which I suspect to be a c function associated to either NSStorage or NSRunStorage.  NSLayoutManager has 5 instance variables of class NSStorage or NSRunStorage:


*NSStorage *_glyphs;
*NSRunStorage *_containerRuns;
*NSRunStorage *_fragmentRuns;
*NSRunStorage *_glyphLocations;
*NSRunStorage *_glyphRotationRuns;


Perhaps the layout manager is not using a good DataStructure to hold onto its glyphs, though the structure for the underlying characters is fine?

See also CFStorage : < http://gobsd.com/code/darwin/CoreFoundation/Collections.subproj/CFStorage.c >

----

Another good thread < http://lists.gnu.org/archive/html/gnustep-dev/2003-02/msg00087.html >

----

It seems odd that they are willing to cripple the system for the sake of an exact scrollbar reading. An approximate scroll position would probably work just as well and wouldn't make the text system grind to a halt so easily.

*Well, sharking indicates that this isn't the holdup unless the text file consists of one huge line of text.*

----

Okay, I've read the links provided - thank you! Let me get back to the original question, though - but focusing on RTF rather than simple ASCII. How can we cram larger documents into the text system? Would breaking it up into different containers (say, page-sized) solve the problem the sharking has uncovered, then?

----

Maybe!  The trick with that approach would be in giving the layout manager less to deal with.  In the docs, multipage renderings are managed with a single layout manager, which would not get you an improvement unless there's some under-the-hood optimizing that takes advantage of features of this case.  Please be sure to post back if you do try this.

If we think the problem is glyph storage, then we can attack it by subclassing NSATSTypesetter and overriding the glyph storage methods (which by default call back into NSLayoutManager) or by subclassing NSLayoutManager and overriding the methods there.  The problem is that this may be complex, and from what I can tell cocoa *is* pretty optimized.

