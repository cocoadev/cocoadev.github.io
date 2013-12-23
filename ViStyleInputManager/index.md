---
layout: page
title: ViStyleInputManager
---



Inspired by http://aldebaran.armory.com/~zenomt/macosx/General/KeyBindings.html and my continual frustration of not being able to use my instinctive vi editing habits in OS X apps, I've been thinking about writing an General/InputManager (at least I suppose this is the way to do it in Cocoa) to implement vi style command mode and mode switching (and binding General/NSRepeatCountBinding to any numerical input while in command mode).

As an experiment, I've created a fairly comprehensive (though still not quite complete) General/KeyBinding file for simulating vi command mode, and it's quite evident that General/NSResponder already implements almost every text editing function I need.

So now the biggest question is: how to go about implementing an General/InputManager that catches certain keypresses ( Esc, ^[ ) and "switches" the keyboard into mode where most alphabetical keys are mapped to editing functions, and goes back to normal text input when the user presses inputs an 'i' - all while not interfering with keyboard layout switcher and Kotoeri et al.? I've read the API reference and tried to have a look at various examples, but I'm still a bit lost on where to start.

----

You might want to check out a project called General/VIMim which was a hack put together by Robert Chin and Nicholas Riley at General/MacHack 2002. There used to be some images at http://web.sabi.net/log/stories/2002/06/24/vimimScreenshots.html (the images appear to be missing, but are available in the Internet Wayback Machine http://web.archive.org/web/20060214105202/http://radio.weblogs.com/0100148/stories/2002/06/24/vimimScreenshots.html). It was put together pretty quickly and would crash not long after using it (mostly due to some issues created to get it working as quickly as possible), but it did work to input text, as it copies the buffer directly into the outputted text.

Probably what you want to do is to emulate something similar to the Chinese text input method that uses pin yin input. In that input method you type something in romanized characters (such as "hao"), a list of possible candidates appear, then when you hit the number corresponding to the correct choice it inserts the appropriate character. You won't be able to get stuff like visual selection, but you should be able to simulate turning off text input and entering your command buffer.

----

There is an ongoing project that began in Sept. 2006 to do just this.  It is called General/ViInputManager and can be found at http://www.corsofamily.net/jcorso/vi .  It patches the Cocoa text system to add Vi-like functionality to all Cocoa text boxes and fields.
