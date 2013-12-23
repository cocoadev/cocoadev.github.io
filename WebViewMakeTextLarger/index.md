---
layout: page
title: WebViewMakeTextLarger
---



Ok, so I'm struggling with this one. It seems that there's a built in action in General/WebView... I've dragged the General/WebView control to the main window... instantiated an instance of a controller class based on General/NSObject. I am loading pages and all works great... but I want to add menu items to increase and decrease text sizes. So I added an Action to my controller, connected it with the menu item but [webView makeTextLarger]; gives me one of those "selector not recognized" errors. Checking the documentation, it states that it provides an action and it's not a function per say... 

But how do I access one of those built in actions from my controller class? If I try connecting my menu item with the General/WebView item in IB it only shows the methods for the controller.. not the built in actions...

Any suggestions? 

----
Add the actions to the General/FirstResponder "class" in your General/MainMenu.nib, then connect your menu items to that. At runtime, when the General/WebView has focus, your items will General/JustWork.

----
Seems my first responder is my controller class... Do I just delete my defined methods? I guess I have totally screwed something up.

----
In General/MainMenu.nib, you will see an object called "First Responder". Double-click it. This will select the General/FirstResponder "class". ("Class" is in scare quotes because there isn't actually a class called General/FirstResponder, it's just a placeholder.) Add your actions to that. Then connect your menu items to this First Responder object.

----
Awesome. Thanks so much for taking the time to help. It makes more sense now too. I'm going to have to do some reading on that so that I really grasp how it works. 

----
Follow the links to General/FirstResponder and General/ResponderChain to see what's going on here, and poke at the relevant sections of the docs on those subjects.
