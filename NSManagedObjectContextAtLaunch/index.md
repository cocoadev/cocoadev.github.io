---
layout: page
title: NSManagedObjectContextAtLaunch
---

Can anyone tell me how I can retrieve objects from the NSManagedObjectContext at launch? I am trying to get the objects that have been saved before and count them. I have already tried -(void)applicationDidFinishLaunching:(NSNotification *)aNote. If there is a way to get each NSManagedObject that has been saved after the application has launched please do tell! Thanks!

----

I don't know squat about this subject myself, but a cursory search of **this** site on the term NSManagedObjectContext yielded a couple of handfuls of hits. Did none of those pages shed light? For example, PhilosophicalCoreDataProblem.

----

You first need to get the context. If you created the app, you should know how to get it. If you used Apple's template for an application, you can call managedObjectContext on the app delegate. If you used Apple's template for persistent document-based app, then each document has a separate context, so you can't just get them at the application level, you need to load documents. Sorry if the answer is confusing, the question is not super-clear ;-)   --CharlesParnot

