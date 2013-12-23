---
layout: page
title: CoreDataMultipleUsesOfData
---


How can I use data in a popup multiple times, but with different content sets?

The Problem. Lets say I have a list of people that I want to assign to a project. A project is made up jobs, which each require certain tools. For example, Job1 may require a hammer and saw and Job2 a hammer and screwdriver. I want to then assign these people to a certain tool within the job. But either my datamodel is messed up or I'm having trouble figuring out the bindings.

For example, with these datamodels (removed the properties for simplicity):

** (entities) **

* (relationships)


** Project **

* jobs (to-many P<nowiki/>rojectJob)


** P<nowiki/>rojectJob **

* project (to-one Project)
* job (to-one Job)
* workers (to-many P<nowiki/>rojectWorker)


** Job **

* projectJobs (to-many P<nowiki/>rojectJobs)
* tools (to-many Tool)


** Tool **

* job (to-one Job)
* tool (to-many P<nowiki/>rojectWorker)


** P<nowiki/>rojectWorker **

* job (to-one P<nowiki/>rojectJob)
* person (to-one Person)
* tool (to-one Tool)


** Person **

* workingOn (to-many P<nowiki/>rojectWorker)


I think that's all of them. My main problem is the Tool. A tool should be set as part of a job (ie. Job1 has a hammer and saw). Jobs never change, Job1 always requires a hammer and saw and can be in many different projects. Then for a project, it gets assigned Job1. Workers should then get assigned to Job1. No problems so far, but now a worker should be assigned a tool from Job1 (and some information about when it was used, etc.). The problem is the Tools have bindings for the content set of the selected Job (because tools need to be assigned to jobs). But then how do I also get a Tool object with the content set of the selected Job from a project (P<nowiki/>rojectJob)? I've tried a seperate array controller, but that doesn't work. And I've tried a fetched property, but I'm not sure how that would work.

If needed I can probably throw a project together really quick to show what I'm after.

Thanks

