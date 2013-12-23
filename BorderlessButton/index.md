---
layout: page
title: BorderlessButton
---

hey i was just wondering how i could make a borderless button with a custom image in it and a custom size.  I already know how to make a button with an image in it, by subclassing NSButton.  But i was just wondering how i could get rid of the bezel and border that the buttons have.  i was also wondering how i could make the buttons different sizes than the ones available by default in IB.  Maybe there is a different pallete i have to load or something, but i haven't found any information on that.

Sorry if this is kind of a newbie question.  I just haven't been able to find any information by surfing the web.
I would also like to have this working on 10.3 too.
----
Don't worry, this is VERY easy. Just drag a normal NSButton into your IB window, click on it, and change two things in the inspector. Set "Type" to "Square Button," and uncheck "Bordered." Then use the size handles to resize the button. It's that easy! You set images the same way as you always have: by entering the image name in the "Icon" field, or by dragging the image onto the button itself. --JediKnil

----
Alright i would just like to say i am blind!  Thanx for the help. Thats exactly what i needed.

*Good to know! Discussion retired.*

