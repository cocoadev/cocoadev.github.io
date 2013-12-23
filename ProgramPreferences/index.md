---
layout: page
title: ProgramPreferences
---

I'm having some problems using preferences in my program. Right now my program has a Controller class and a PreferenceController class. The PreferenceController is a subclass of NSWindowController so I can save the window's frame. My problem is I don't know what code belongs in what class or if I should just switch PreferenceController to a subclass of NSObject. In my PreferenceController class, right now the only preference I am working with is choosing an option for a Rendezvous service. The delegate methods for NSNetService and NSNetServiceBrowser are in the PreferenceController.

Once you open the program, it goes into the user defaults database and finds out which Rendezvous service you want to connect to. Before I can use this service, I have to wait for the service to be resolved. When it is finished being resolved, service's information to interact with it. The delegate methods for NSNetService are in the PreferenceController class so I must load the PreferenceController class which loads a nib file. The nib file might not be needed in this use of the program so it is a waste to load it into memory. Should I make delegate methods in the Controller class for NSNetService even though I might be rewriting code that is written in the PreferenceController class? Should I just change the class of the PreferenceController class to an NSObject so if I load that class, I won't be loading the nib file until I tell the PreferenceController to?

----

The NSWindowController should not load the nib file before you send it a request for the window. However, I think that having the preference controller both service as a delegate for NSNetService and handling the preferences window is not the way to do it -- perhaps create a new class which is the delegate for NSNetService, and let the preferences window controller use this class (as you state something about duplicating the code in the controller otherwise).

