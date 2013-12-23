---
layout: page
title: SliderWithBindings
---

Hi, I have a slider that is connected to text while using bindings. The slider controls the textSize of the text. The slider does work with the bindings this far. I am also creating a 'restore all settings' button. I have put this code into the code for the action of the restore button,       [textSize (the slider) setIntValue:16];   **the "(the slider)" is not part of my code** That code does change the value of my slider, BUT does NOT change the text size. How do i change the size of the text while changing the value of the slider?

I have tried many different combinations, too many to count and to type, so anything would be appreciated. If you dont understand what I have said, feel free to email me, mamiller93@gmail.com thanks!

----
Sliders are for the user to manipulate, not you. If you want to change this value, change the value on the other side of the binding. In other words, change the actual text size of the text. The binding will then cause the slider to update to reflect the new value.

----
Your slider's value and your text view's fontSize should both be bound to some key in a controller or model object. Let's say the key is 'textSize'. If you need to be able to change the value associated with that key programmatically, the simplest thing to do is to make sure that there's a KeyValueCoding compliant setter method for that key, i.e. you need to implement -(void)setTextSize:(int)size in whatever class is home to the textSize key. Make sure that whenever you programmatically change the value associated with textSize, you do it by calling the aforementioned setter method: [self setTextSize:42]. If you do that, Cocoa's system for automatic key value observing will notice that you called the setter for the textSize key and will automatically notify anyone watching that key (the slider and the text view, in your case) that the associated value has changed, so the slider and the text view will both update themselves appropriately. -CS

----
Or you could just set the text view's fontSize value directly, and things will take care of themselves.

----
THANK YOU SO MUCH!!!

