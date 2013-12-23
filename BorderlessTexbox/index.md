---
layout: page
title: BorderlessTexbox
---


I'm trying to create a simple textbox window that's borderless and has no titlebar. I've tried all the methods posted here but won't work when I run it. I get a borderless white box with no titlebar, but I cannot enter text into it or move it around. I'm sort of new to this and I need some help

----
HowToAskQuestions - Learn how to ask pointed questions and post what you've tried so far so you're not expecting people to consult their crystal balls.

MailingListMode - Learn how to avoid abusing this wiki and the community that maintains it. This is *not* a mailing list.

(edited for clarity)

----

Question: how are you going to enter text AND move it around? A mouse click in a text view will set the insertion point/selection, OR it will allow you to drag the window if you set it up that way - but not both. That's why windows have title bars --GC

----

hmm, alright. I see what you mean. can I have it so that it has no borders and I can still enter text, even if I can't move it around?

----

Yes, you can. (HowToAskQuestions.)

----

You can even set it up so that when the user holds down the cmd (/apple) key they can drag the window around too. See also BorderlessWindow.

----

well, you mind telling me how to do it instead of just saying it's possible?

----
Can you ask a decent question instead of expecting everyone to read your mind?

Please, please, please read HowToAskQuestions. We don't keep sending you there just for our amusement.

----

I must've read that page twice through already. I'm sorry if I'm coming off a little annoying or something, but you don't have to act so elitist. I'm just looking from some help is all. What do you want me to do, post some of my code? Because all I've used so far was the same code from WindowsWithoutDecoration and BorderlessWindow. I didn't think of the fact that these wouldn't work on a text box because of the limitations of how you click on it and how the window would be selected, but somebody posted it's possible to use the cmd key to add functionality. All I need to know is how to do that and I'll be on my way.

----
Just be more specific about what you want to do.

You want to put up a borderless window? BorderlessWindow

You want to put a text field in it? Load the window from a nib, or add one programmatically.

You want to make it draggable by holding the command key? Override     mouseDown: to check for flags, and start moving the window if command is down.

Your question is just too big. You need to break it into pieces like the above. Then ask *specific* questions about the pieces when you have trouble, and you can get help.

----

Alright, sorry for not specifying in the first place. I guess it's because I don't really know much and I didn't know how to get more specific.
Thanks for the help.

