---
layout: page
title: TellWhenAllAwakeFromNibsCompleted
---

Hi,

My application has several General/NSObject's instantiated within Interface Builder that act as the controllers for the different parts of my app. Pretty simple stuff.

All of these controllers have awakeFromNib methods which handily set up the controller for proper use.

One piece of functionality is that the app will load the file last loaded on startup. However this is causing all sorts of problems, I can't figure out in which awakeFromNib method to put the code that loads the last-opened file.

No attempt to open a file should be made in the app before all the awakeFromNibs have been called (or horrible SIGSEGV errors abound as could be expected), but what is happening is that I am putting the last-file-load code into the awakeFromNib in the controller I (conceptually) think of as the main controller, but this throws the aforementioned errors when you try to do certain things in the app, because it is loading the file before all the other controllers have had a chance to awake and set themselves up.

So my question is this: how can I wait until all awakeFromNib's have been called? How can I then run this code that opens the last viewed file? Where do I put this code? Etc.

At first thought I suppose I might hack together some kind of method that receives 'awakeFromNib done' messages from each of the Controllers, and then when it's received work from all of them it'll run the code, but this seems a messy hack to me...

Any thoughts / help much appreciated,

-Peter

----

Make one of your objects the application's delegate, and put that code in the -applicationDidFinishLaunching: delegate method.  That method will always be called after the main nib is finished loading.  -- Bo

If that's too late, put it in the -makeWindowControllers: method -- General/BrentGulanowski

----

Thanks very much for your help - much appreciated!  -Peter
