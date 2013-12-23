---
layout: page
title: GhostTasks
---

I have a very bizarre problem and the solution has eluded me for a week or so now and i can't figure out what is going wrong. In a nutshell the application processes through a list of items and launches unix binaries to accomplish its tasks. One task pipes to another and the second task creates the output file. (we'll call them task_one and task_two)

In the main class we create the dictionary with all the information to be passed to a sub class that handles all the processing. (im sorry im awful at describing how code works)


anyways- we loop through all the files to be processed and relaunch the unix binaries each time to process the new items. sometimes it will launch a ghost version of the application instead of one of the desired tasks. 

so for instance the first time through it will launch task_one and task_two and go on its merry way until the item is finished being processed. it will move on to the next item and instead of launching task_one it will launch a "ghost" version of our application and task_two (task_one never gets launched, its as if the ghost version of the application is being mistaken for task_one) 

the worst part is it never finishes the task when this ghost version is launched effectively putting a hold on the whole process until the ghost version of the application is forced quit.- i wish i could better explain the problem its just so obscure and i don't truly understand what is happening, im wondering if anyone else has ever had a similiar issue.

