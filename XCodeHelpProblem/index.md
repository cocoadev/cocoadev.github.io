---
layout: page
title: XCodeHelpProblem
---

I posted a question about this at the Apple Support discussions as well. I recently upgraded to 10.5.1 and General/XCode 3. Now that I've switched to General/XCode 3 I have a problem with my application's help file. I edited the help.html file for my application. In General/XCode 2.x I had to clean the project and rebuild it so that it would display the new help file in Help Viewer when I used the application Help menu item. Now I can't change the help file to the new one. I've tried everything to get the project to use the new helpfile.html:

I've removed the Help Folder from the project, cleaned the targets, and removed the General/CFBundle attributes General/HelpBookFolder and General/HelpBookName from the info.plist. Then when I rebuild and run the project and use the help menu the Help Viewer reports "Help is unavailable for the application". This is correct. Then I clean the targets, add the General/CFBundle attributes for the help file, add the Help Folder to the project. When I build and run the project the application's help file is the old one, which doesn't exist any where in my project folder but General/XCode seems to have retained it somewhere invisibly in the project file. Does 10.5.1 cache this in a library or something? Is the Help file from the release version being used (I haven't updated the release version yet)? Any ideas?
