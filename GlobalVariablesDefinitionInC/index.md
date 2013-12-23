---
layout: page
title: GlobalVariablesDefinitionInC
---

Well I deleted my original question, as approximately 5 minutes and 2 aspirin after typing it I came up with the solution. So instead of a question, I'll post the solution I am using.

Problem:
When using external (global) variables in C, how can the variable definitions be centralized into a single file for code upkeep?

Declaration:
    
extern char buffer[512];


All global declarations are stored in a file, data.h. One file, every global needed.

How then could the definitions for each of the globals be similarly centralized outside of main.c?

My solution (I are smart):
Make a file, data.c (or whatever you want to call it). Add the definitions to this file.

Definition:
    
char buffer[512];


Then go to the Target tab of the PB and drag the source of data.c to the top of the Sources list.

This simple solution worked for me and hopefully is the correct way to do this.

Ned

----

This is basically how it should be done. You aren't required to put them in a separate file, of course, but there's no reason not to if you think it's organized better that way.

