---
layout: page
title: NSTextFieldSelectionBehavior
---



Having spent a couple hours with my program last night debugging some complex geometric calculations, and needing to repeatedly enter numbers into text fields and drag sliders and whatnot, I realized that the behavior Flash MX exhibits -- where clicking in a text field automatically selects the text -- would be very useful and user friendly.

The default behavior seems to be to select the text if the field is tab-selected, e.g., you tab over to the field and the text is selected. But if you click in a default NSTextField the text isn't selected, rather the curser simply shows up approximately where you clicked. To select the text you have to double click, or do a selection-drag. While this is expected behavior, it's helpful in some circumstances to have the text simply select automatically -- and this is what I want.

It tried to implement my desired behavior by subclassing NSTextField and overriding NSResponder's becomeFirstResponder: method. I had it call selectText: on itself, but it had no result.

Am I taking the wrong approach? What do you recommend?

--ShamylZakariya

----

I have done this in the past. My solution was to override mouseDown: in my NSTextField subclass and select all of the text after calling super. Note this approach doesn't fire when tabbing, but the tabbing behavior is already correct. This part of the code is a little squirrely -- if that approach ends up not working for you, let me know and I'll put together a second, more obtuse, example.

-- MikeTrent

Hey, thanks! It worked beautifully. If I were concerned with really proper behavior I might do something like only selecting the text if the field is gaining focus on being clicked, but I'm not concerned, here.

--ShamylZakariya

----

Mike, could you tell us about your second, more obtuse, solution?

I once tried to override becomeFirstResponder in order to have the text select even if the textField is gaining focus programmatically; I used a runLoop trick I found here.
But I�m not sure, in such circumstances, if I should return YES, call [super becomeFirstResponder] ?? and in that case if it must be called before or after doing other operations (like an NSLog�)?

Any comment??

--Flofl

