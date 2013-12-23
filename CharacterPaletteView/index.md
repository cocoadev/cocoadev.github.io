---
layout: page
title: CharacterPaletteView
---

I'm writing a simple program to help myself (and eventually others) learn the Japanese Kanji, and I'm looking for a view similar to the one in the character palette. I was thinking maybe I'd want something a bit like the icon view in finder, but I can find no mention of either of these views anywhere. I tracked down the character palette in the System folder, but alas it does not have a nib file I can examine :( Since I'll be wanting large, hi-res character glyphs in each of the cells, will I be needing ATSUI? The cells do not need captions, unlike the ones in the finder's icon view.

Does anyone have any suggestions? 

Thank you :)

PS: By the way, ideally, I'd like it to support bindings, but from what I've read, it seems that custom bindings are hard to work with (at least in IB,) right? Drag and drop? Basically, I was wondering if there was some pre-existing cocoa-native view that did what I wanted, but that just wasn't available in IB.

----
Without thinking about the problem too hard, I would recommend an NSMatrix with custom cells. These custom cells could be NSImageCell subclasses, made to provide the image on-demand rather than creating the images beforehand, where you'd just give it the character to display and it would create the image itself. Since you*d probably have potentially thousands of cells in your view, you'd want to create only the images that were actually visible. Have some custom logic to fill in the NSMatrix, stick the whole thing in an NSScrollView, and you should have something about like what you want. -- PrimeOperator

----
Ok so I have an NSMatrix of NSImageCell<nowiki/>s, but I'm not sure where to go from here. If I make it a list mode NSMatrix, I can select the NSImageCell<nowiki/>s, but only if their border is visible, which is not what I want. I'd like the NSMatrix to behave a bit like it does in IB (or, in fact, the character palette,) where I can select an entire cell and the background changes. Is that a default behavior or do I need to subclass/delegate? How can I get drag&drop capabilities into the NSMatrix? How would I go about binding the images to objects? Should I just have a character glyph in my coredata model, and then make a mutator that generates the image from that? I'm not quite sure how to do that, if so. I have the NSMatrix in a scrollview, but am not sure how efficient it would be to have an NSMatrix of many thousands of cells inside a scrollview. Is it smart enough to know that it shouldn't ask for the image of the invisible cells?

----
Thinking more, the NSImageCell idea is probably not so good. An NSTextFieldCell should be able to do what you want, since you are basically just drawing a one-character string, and that way you don't have to worry about images or caching or any such crazy stuff.

You will have to subclass the NSMatrix for drag-and-drop. You may have to subclass NSTextFieldCell to modify its selection behavior, but I'm not sure. The NSMatrix will be smart enough (I hope!) to not play with cells that aren't relevant, so only visible cells will be asked to draw. -- PrimeOperator

*You might also want to look into DragMatrix, and use NSButtonCell<nowiki/>s for the highlight capabilities. Or, if you want to go back to your original idea, maybe you should try UKDistributedView. --JediKnil*

----
Aha! The NSTextFieldCell seems to be almost exactly what I need! I now have an NSMatrix of them, and in list mode it looks pretty much like what I need. One thing I've noticed. There seems to be some strange selection behavior. If I click on a random cell, it won't select. However, it starts out with the 0,0 cell selected, and if I move the selection with the direction keys on the keyboard, I can select all the cells. If a cell has been selected in this manner, I can now select it with the mouse, too, but otherwise it just doesn't do anything. Anyone have any ideas?

----
Well, after several months of not touching the code, I'm interested again. I dug around in CharPaletteServer and found that it's cocoa after all, and uses a custom view called CharMatrixView. In the meantime, I tried creating an NSMatrix of NSTextFieldCells in an NSScrollView and binding them to the arraycontroller that deals with my characters. However, for large numbers of characters it crashes unpredictably, which leads me to believe that it's not being lazy and showing only the stuff currently in view. If I were feeling ambitious I'd create an NSMatrix subclass that only has enough cells in it to fill the view, and move the contents of said cells around (and the cells themselves, to pretend I actually have more cells) depending on the NSScrollView's position. However that seems like an awful lot of work for something that seems rather basic. Does anyone have any better suggestions, before I dive in and implement the above?

Thanks,

DanielPeebles

