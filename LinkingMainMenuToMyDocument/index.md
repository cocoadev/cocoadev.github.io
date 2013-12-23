---
layout: page
title: LinkingMainMenuToMyDocument
---

I'm trying to add a Cocoa Document based application to an already existing project. I've included all the necessary source and nib files. My newly added target compiles, but the produced application only seems to load the MainMenu nib seems to load. I was wondering if I need to some how explicitly link the MainMenu and MyDocument nibs together? Is this something the Cocoa Document-Based application template normally does?

Any ideas?

Thanks,

MacHead4Ever

Nevermind folks. Forgot to set the document types role to "editor". Doh.

