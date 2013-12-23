---
layout: page
title: FilteringArraysUsingNSArrayController
---

I'm trying to create a to-do list application that displays a list of tasks for a given day. Tasks should appear from they day they were created until the day they're checked off, and I have a nice function that checks for this and does the right thing. This is all well and good, but my problem is how I should display this.

The first thing I tried was creating two array controllers, one for tasks and one for days. Tasks were bound to days and by switching through days you can see each task. This works great if tasks are created and finished on the same day, but they don't carry over to the next one.

Being a newbie, I'm not entirely sure where to go from here. I'd like to avoid using table data sources because that always felt clumsy and glue codey to me. I saw an entry in bindings that caught my eye, however -- Controller Content Parameters. Is there a way I can use this to control how tasks are displayed?

All the tasks are stored in one big NSMutableArray. Each TaskItem has a variable for startDate and completion. It also includes a method that runs some checks to see if the task should be shown on a provided date. Ideally, when looking at a day for the first time, I can run through the task list, run the method that checks if it should be displayed on that day (returns a BOOL), and if it that method returns YES, display it in the table. Can I use the filter predicate for this? Or am I barking up the wrong tree with array controllers altogether?

