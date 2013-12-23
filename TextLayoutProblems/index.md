---
layout: page
title: TextLayoutProblems
---

Hey, all!

I'm trying to do my own sort of Sketch app (Examples/General/AppKit/Sketch) just to try to learn it all. I managed, by myself, all the way through rectangles, ovals and pictures, but when I started to make my own text-thingy I thought I needed some help. So, I looked at the code in the Sketch app and rewrote it some to fit the structure of my own code, while not � as far as I can tell � changing the important parts. But when I ran the program, the text � both while I was viewing it, and editing it � was always in the lower left corner of the text-rectangle. Which was quite annoying. Since I could not fix this problem, I deleted all that crap-code (=P) and started writing it all on my own.
  "Wee!", I said when I made "Hello World!" visible in my General/NSView. But.. The joy didn't last too long. When I resized the text-rect so the "Hello" and the "World!" need to be on two different rows it becomes like this:

    
World!
Hello


Which is completely wrong!  And when I shrink the rect even more, the text is drawn like this:

    
!
d
l
r
o
W
 
o
l
l
e
H



Why? I cannot seem to figure this out!  Please, help me. I would be so grateful!


  Huge thanks in advance.


Yours sincerely,
  General/AntonKiland, Sweden.

----

Have you tried flipping the view?

-- General/MikeTrent

----

That could fix the problem.. Only thing is:  How to flip it? =)

Thanks!

-- General/AntonKiland

----

Implement - (BOOL)isFlipped and just tell it to return YES. That is to say, add this to your General/NSView subclass:

    
- (BOOL)isFlipped {
	return YES;
}


-- General/JustinAnderson

----

Major thanks!  It works now =)

-- General/AntonKiland
