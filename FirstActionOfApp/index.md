---
layout: page
title: FirstActionOfApp
---

Hi, I'm having a small problem.

I'm trying to get my program to execute a function in the controller immediately after launch, and I tried setting the controller to the window's delegate and then using applicationDidFinishLaunching but that didn't have any effect.  How do I make this function work?


----

You need to be the application's delegate to receive that message.  If you're the window's delegate you can implement -windowDidLoad and it might be close enough.  If not, create an instance of your class in your MainMenu.nib and hook the File's Owner delegate outlet to your instance, and then you'll receive the applicationDidFinishLaunching method. -- Bo

