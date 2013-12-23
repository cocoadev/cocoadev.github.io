---
layout: page
title: BindingNSArrayControllerContent
---



I am building an iTunes-like browser for my biblographic app.
I have 3 columns (topic, author, text) and a list of passages, just like the iTunes model.

http://www.cnomania.it/garage/browser.jpg

Now, I have managed to sort the Authors column according to the selction in the Topics colums, by binding the contentSet of the authorsBrowser to the topics relationship.
The thing is,now, I must restrict the content of the 3rd controller (texts) taking into account the selection of BOTH preceding column; ideally, I should bind the content set to the 'entries' relationship of BOTH the topic controller AND the authors controller. This cannot be done through the contentSet binding.
Should I bind to some filterPredicate? Which one?
Any suggestion is welcome.
Cheers,
Davide

