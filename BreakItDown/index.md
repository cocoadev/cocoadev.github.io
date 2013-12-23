---
layout: page
title: BreakItDown
---

BreakItDown is one of the fundamental processes of programming.

Much of programming consists of breaking larger problems into smaller problems. Large programming problems are impossible to solve on their own. It is vitally important for every programmer to be able to break these problems down into smaller problems, then break those smaller problems into still smaller problems, on a continuing basis, until the individual problems can be solved directly in code.

Here's an example. Let's say you want to write a chat program. "Write a chat program" is a huge problem, impossible to solve. So you BreakItDown:


*You can think of a chat program as a GUI and a network backend.
*The GUI consists of menus, preferences, a buddy list, and individual chat windows.
*The buddy list needs to show a name, icon, state, and tag line for each buddy.
*NSTableView can be used for the list, then a custom NSCell for the entries.
*The custom NSCell needs to draw a couple of images and two strings.
*NSAttributedString can be used to draw the strings as needed.
*Finally, something we can code.


Of course when you're really writing this program, you'll need to apply BreakItDown to *all* of the problems, not just following one particular problem to the bottom like I did in this example.

This is also very relevant to HowToAskQuestions. Since these larger problems are impossible to solve, questions about them are basically impossible to answer. A question such as "How can I write a chat client?" will not get any good answers. But if you BreakItDown first, then ask intelligent questions about how to draw strings, talk to the network, etc. then you will get a a lot of good help.

Odds are that if you apply BreakItDown well, you'll find yourself asking for a lot less help to begin with. Since the smaller problems are easier to solve, you're more likely to be able to solve them on your own, which makes your job a lot easier!

----
**Discussion**

Very well-written and quite appropriate. If someone has directed you to this page, you should be thankful for the sound advice and follow it to the letter (as well as the reference to HowToAskQuestions).

