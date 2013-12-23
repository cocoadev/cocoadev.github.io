---
layout: page
title: WritingGamesUsingQuartz
---



Hi! I'm looking for info regarding writing a 2D shootergame.
My question is: can the game be fast and "snappy" if I try to write it with Quartz?
I've looked into using General/OpenGL but I would prefer to learn more about Quartz and Cocoa in general
The game will be a multiplayer, realtime actionfest so speed is crucial

EDIT: For those interested, I'm thinking about some kind of clone of this great game: http://www.soldat.pl

Can it be done, or is General/OpenGL the only way to go? Hope not :P

Andr� Lind

----
First of all, I would really suggest aiming lower. That's not to say that you should give up on your game, quite the contrary. But don't jump into your game just yet. Make a screensaver with some bouncing balls. Write a Tron clone. Do some simple stuff first, this will teach you the basics, show you the advantages of various approaches, and keep you motivated as you'll see results right away. (Note, if you've already written something fancy on other platforms then a lot of this becomes silly, but I'm guessing from your tone that you have not.)

As for your actual question, Quartz is certainly doable for an action game, as long as the game is adequately simple and your system requirements high enough. But General/OpenGL will quite literally be ten times faster or more for this sort of work. The question becomes whether your game is complex enough that it needs the speed. Don't be afraid to experiment, just try to leave your code flexible enough that you can change approaches without an enormous amount of work.

----
I encountered this same problem a few years back when I was writing a game engine (http://www.pixelballistics.com/Software/Alea/Contents.html ). The short answer is that Quartz will be fine, but a little slower, if you're making a 2D game. If you plan to go 3D at any stage in the future, I recommend that you start with General/OpenGL from the very beginning. The differences between the two systems will make it a real pain for you to go back and switch everything later. The other thing to keep in mind is that General/OpenGL integrates just fine with Cocoa, so there's no reason why you can't use Cocoa for the user interface and data modelling, and General/OpenGL for the game display.
--General/ToM

----
Thanx for your opinions!
I think I will go the General/OpenGL way... If the game doesn't get the speed I need I wont have to rewrite a s**tload of stuff :)
