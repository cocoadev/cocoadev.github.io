---
layout: page
title: DeterminingWhatTheUsersInputMenuIs
---

CocoaDiscussions - Determine Input Menu Setting

Hello,

  I would like to know how it is possible to determine what the user's current input menu setting is? I need to limit the user of my application to only input single byte characters. The only way I can think of doing that is to check first what input menu the user is currently using. Then if I determine that they're using, for example, Katakana, I'll just limit their input to digits/numbers.

  I am using NSCharacterSet in my source code to limit the character set that I allow my user to use. However, the user can still input "aishetemasu" in English text with Katakana input menu and get the Japanese character equivalent.

  I hope you can help me. I couldn't find any information in the Apple Mailing Lists.

Thanks.

----
You should just write an NSFormatter subclass to apply to your text field to limit the user's input.

----
Thanks for the suggestion, but I would still like to know if there is indeed a way for a programmer to determine the current input menu setting via Cocoa API?

----
If there is then it would be in NSInputManager, but I don't think that class actually works much anymore.

----
I have already tried to create a custom formatter to limit the user's input. However, when I change the input menu to Katakana or any of the Japanese input methods, verification if string is valid is not done per keystroke. I noticed that if I enter a set of characters and add a space, that's the only time the validity of the string is checked.

  Is there a way for me to still check the validity of the string I entered even if the notification does not work?

----
I hesitate to say this since it's not really a positive contribution, but what you're doing is very un-Mac-like. Japanese is not the only input method that uses the "decision buffer" of characters before the input is sent to your text field, and users can add their own input methods. Which means that you'd have to have a whitelist. Which would bother people with legitimate other input methods (say, Dvorak or Colemak) that you might have left out. Unfortunately, because the kana translation is done at the input method level, AFAIK there's no way to catch the characters before the space or return. I think you should just validate the entire string at every input...users that use Japanese or other inputs *probably* know what's going on. Especially if you specify "ASCII characters only" (which is better understood than "single-byte characters"). And last, I have to say, why only single-byte characters? I've even seen CreatorCode<nowiki/>s that use UTF-8 to represent three of the four bytes. --JediKnil

----

If you ask me, discouraging programmers (or people in general, for that matter) from doing something stupid is always a positive contribution. We should feel no obligation to help somebody do something just because they asked about it. If their goal is stupid then we ought to say so, and encourage them onto the right path. -- MikeAsh

