---
layout: page
title: NSToolbarValidateItemImmediately
---

I have a NSToolbarItem that I need to disable immediately when a particular condition occurs (it's a stop button depending on whether a thread in my app is active). How could I do this? The docs say not to call NSToolbar's validateVisibleItems directly but if I let it alone the stop button stays active for a few seconds after my thread has exited.

----

Nevermind... I fixed this. Stupid mistake. In short, I was using ThreadWorker and my thread *wasn't* exiting because I was doing something like this:

    

    while (eachItem = [itemEnumerator nextObject])
      {
        if ( ![worker cancelled])
            {
             // doing stuff here
            }
       }



So the loop would never finish until the enumerator was exhausted. Removing the <code>if (![worker cancelled])</code> and adding a <code>&& ![worker cancelled]</code> clause to the while condition was the solution.

Sometimes it's the simplest things...

