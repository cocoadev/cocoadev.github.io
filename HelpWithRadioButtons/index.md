---
layout: page
title: HelpWithRadioButtons
---

Hi, I'm a beginner cocoa programmer and I'm need some help with radio buttons, I' have tow questions.
How do I create tags for radio buttons using IB?
How do I creat a Matrix of more than 2 radio buttons using IB?

Thanks in advance.

----

To get more rows, hold down the option key and resize the NSMatrix (making it higher), new rows will appear, as space permits.

To change the tag, double click the NSMatrix, which should select the cell, open the Info utility window and at the attributes pane there is a text field where you can enter the tag (the title of this Info panel should be NSButtonCell).

----

**Using radio button tags**

I'm new to objective C and just OS X programming altogether. I used to program java, just basic shit, but, its got me all confused. In java I believe there was a way to get index values and things like that. Basically I'm just trying to learn different aspects of Interface Builder and Xcode..... I created an interface and it had 4 radio buttons. Each one has its own tag 0-3. I created an outlet for each radio button. Thinking that I could then in the code say..... if radiobutton Value == 1 then do this and so fourth..... But I can not figure out for the life of me how to figure out which radio button is selected..... I'm trying to figure this all out by myself, and i was searching apples developer site and google, but I cant figure anythign out. I'm sure its easy and right in front of my face, but oh well...... Anyways, incase im horribly off with everything. Can somebody just tell me what I would need to do to have a program with 4 radio buttons and to be able to tell which one was selected......

----

Do you have them properly connected to your class in Interface Builder? Without this, you can't easiliy find out about any controls or objects in your window. You need to create an instance of your class via Interface Builder, and then connect the outlets in your class to the radio buttons in your window. If you're unsure of how to do this, you might want to read up on a few of the pages at HowToProgramInOSX (more specifically HowToUseOutlets).

*Well, first of all, make sure you know basic IB and Cocoa stuff. You can screw things up horribly if you don't know any Cocoa, (I know; I went through the exact same transition), especially MemoryManagement. Basically, what you want to do here is to connect the radio buttons (or the NSMatrix containing them) to an action in your controller class. Then in your     .m file, implement the action as if the sender was the containing     NSMatrix. This means that you can call     selectedCell to get the radio button instance, or     selectedRow to get the row that was clicked. If you need more in-depth instructions, look around at HowToProgramInOSX. Oh, and can you try to use good grammar and spelling (and not swear)? No offense meant -- it just always helps when other people can learn from your solutions, and a nice page aids that process. Good luck! --JediKnil*

----

I did create an instance of the class and I did have them connected properly to my class. I just didn't know the correct syntax... selectedCell and row to find out which one was called.... I'll work on this more at work and see if I have any trouble..... but question,m in the beginning you say to make sure its linked up to an outlet properly, then you say action..... which one should it be?

----

A quick and dirty way to determine if your outlets are properly connected, is on     awakeFromNib NSLog your outlets:     NSLog(@"%@", radioButton1); and if it shows (null) then it's not setup properly.

----

AHHH YES!!!!! thank you guys!!! All I need was to figure out how to call the selected row.... which I did with this call.......radioSelection = [radioButtons selectedRow];....... Then it pulled up the correct tag number and im golden now. Thank you so much guys. I'm sure I'll be back to ask more questions! :)

----

Use [radiosButtons selectedTag] to get the selected radio button tag.

