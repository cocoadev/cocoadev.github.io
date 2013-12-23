---
layout: page
title: SimpleSolutionsNSColor
---

  (Begun November, 2004)

**Table of Contents:**

*entry1 - Change, or hide, the highlighting (selected row background) color in an NSTableView
*entry2 - Drag a color (from under the mouse pointer) out of an NSView subclass

----

*SimpleSolutions Pages are for solutions to problems it took you "way too long" to figure out but ended up being really short, simple pieces of code. Whether or not it would take someone else a long time is beside the point. Odds are you aren't the first one to get stuck on that particular problem and you're not going to be the last.*

----
**This page is for NSColor related solutions only**.
----

Feel free to add to this page. *Short*, **tested** code is best. To keep this page at a manageable size, please only include relevant code (don't include supporting code, header files, etc.). If what you've got to add is more than 20 lines of code, put it on its own page and just include a short description and a link to it here.

Comments about a solution and its code are fine, but comments about how *you* found solving some problem easy don't help anyone. At the same time, this page isn't for newbie questions, either. If you need help, please open a new discussions page.

----

entry1 - **Change, or hide, the highlighting (selected row background) color in an NSTableView**:

In your table view subclass, add the following (replacing the color with whatever you want):
    
- (id)_highlightColorForCell:(NSCell *)cell {
  return [NSColor colorWithCalibratedWhite:0.9f alpha:1.0f];
}

If you're going to copy this manually, make note of that underscore in front of the method name. 

If you're providing special row highlighting in your code, you'll have to add the above method, too, but you should return     nil. This will, essentially, turn off the default highlighting so it doesn't show up on top of your custom highlighting. (Tested in 10.3, Xcode 1.5)

----

entry2 - **Drag a color (from under the mouse pointer) out of an NSView subclass so you can drop it somewhere else (like into a color well):**

Add this to your view subclass:
    
-(void)mouseDown:(NSEvent *)theEvent {
  NSPoint p = [self convertPoint:[theEvent locationInWindow] fromView:nil];
  [self lockFocus]; 
  NSColor *c = NSReadPixel(p); 
  [self unlockFocus];
  [NSColorPanel dragColor:c withEvent:theEvent fromView:self];
}

(Tested in 10.3, Xcode 1.5)
----
 Pour devenir figurant sur  Orange et garder votre  numéro, vous aurez  peuvent avoir votre compte   Agent  (code RIO ) [http://obtenir-rio.info rio bouygues]. Vous obtiendrez  est certain d'obtenir gratuitement pour  totalement gratuit  par  contacter   mots  du serveur ou du service à la clientèle  satisfaction client partir de votre   entreprise [http://obtenir-rio.info/rio-bouygues rio bouygues] . Vous ne  mai   obtenir  un SMS  avec vos . Avec  votre propre  [http://obtenir-rio.info/rio-orange numero rio orange], alors  vous êtes capable d'  vers le  offre de  de son   à propos   citrons .

