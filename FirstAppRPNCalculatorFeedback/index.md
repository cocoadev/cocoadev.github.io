---
layout: page
title: FirstAppRPNCalculatorFeedback
---



This application models a Reverse Polish Notation calculator (RPNCalc).  I used the MVC pattern, but not much else like KVO,KVC, or binding.  That might be my next application.

I was hoping that all of you Cocoa wizards out there could look at my first real application in Cocoa and ObjC.   Since Cocoa and ObjC are such powerful language and framework.  I'm sure that someone will be able to give me pointers on how cocoa/objC could have improved my app.
The address to download the dmg is 
     http://homepage.mac.com/fluidic/RPNCalc.dmg

Thanks in advance for all the advice that I'm sure I will get.

-----------------------------------
Suggestions and Constructive Criticism
-----------------------------------

I would suggest making the back button greyed out when there is nothing to go back to.

I would suggest changing the "NewApplication" entries in the menu items to the name of your program "RPNCalc"

----

Thank you for the the suggestions.  I hope some more people will comment on the objective C and Cocoa stuff I did.  I love having code reviews since it helps me hone my code.
----
You may also want to prevent multiple decimal entries on the same line. 5...2 becomes 5, and ..4 is ignored. Either post an error or disable the decimal once one has been entered. Also, the unary minus function seems a little strange (although this may be the way you intended, so ignore it if it is): If you type a number and press the +/-, that number is negated. If you hit Enter, then press +/-, the previously entered number is negated. You can't enter unary minus as a leading character, although you CAN enter it as some other part of the input string. Finally, you may want to remove the Open / Save / Open Recent menu items since they're never available. In all, it seems like you succeeded with your first Cocoa app! ObJC is awesome! -dan (I'm pretty new to Cocoa/ObjC, too!)

