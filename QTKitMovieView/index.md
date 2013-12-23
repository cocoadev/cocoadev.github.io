---
layout: page
title: QTKitMovieView
---



Hi All

I am new Mac OS X and XCode frame work. My problem is in using the QTMovieView in QTKit 7 Frame Work.

My Platform is
Mac OS X 10.3.9
XCode 1.5
Interface Builder 2.4.2
QTKit 7
Quick Time Player Version 7

I have followed the steps in the page 

    
http://developer.apple.com/documentation/QuickTime/Conceptual/QTKitProgrammingGuide/Chapter04/chapter_4_section_2.html#//apple_ref/doc/uid/P40001245-CH209-TPXREF135 

Upto the section Adding Outlets and Actions to The MovieView Object
every thing is OK. As explained in the step 4 of the section I want to
add actions to the QTMovie Object. When I take the attributes pane of
the QTMovie Info window so as to add actions to it every actions in it
is shown disabled. 

My Questions are 

Do I want to perform any extra step so as to add actions?. 

Is there any Licensing issue for the downloaded QTKitFrameWork.? 

Do I want to purchase the Quick Time Player 7 Professional Version.? 

Thanks and Regards 

Xavier 

----

The QTKit framework is bundled with Tiger. It probably won't work with Panther or below, so don't even think about it. And yes it probably would be illegal to distribute it yourself. The Pro version of QuickTime is very helpful, but you don't need it to take full advantage of QuickTime components in your code. It's mostly for the QuickTime Player program, and for using the plugin in browsers. But having Export readily available in QuickTime Player is a very handy thing.

QTKit is also available on Panther if QuickTime 7 is installed. *Ahh didn't know that. Thanks for the correction.*

----

I'm having a problem where users on Panther 10.3.9 can't use my program, and when I try using XCode 2.1 on Tiger to compile with the framework, people tell me it won't launch. If I try to use the 10.3.9 SDK, that won't even compile! How does it still work with Panther? What do I do? -- JasonTerhorst

What do you do? You show your code, tell us what errors you're getting, what Console output is shown when people try to launch it, crash logs, etc.

I no longer have Panther on my machine, and I don't have any console readouts from Panther users, but the downloadable executable (in a dmg) is at http://www.jterhorst.com/ignition/Ignition.dmg. People have told me that it doesn't give any errors, but the app launches, but doesn't show the doc window (it's a document-based app). Strangely, app help and the font panels will work, though. Just none of the functions within the document controller. I do reference QTKit. Am I supposed to call Quicktime framework as well? As far as I know, that's the only Tiger-exclusive thing I use. Everything else is Jaguar and Panther safe (there isn't even any use of bindings). -- JasonTerhorst

You're probably using a method that's not present and throwing exceptions, which will show up in Console. You really need to get some of your users/testers to give you Console output from a run on a 10.3.9 machine to be able to figure out what's going wrong.

----
Suggest renaming this article to indicate it's a discussion about QTKit on 10.3.9..

