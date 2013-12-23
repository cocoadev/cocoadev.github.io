---
layout: page
title: HowMuchOfAFrameworkIsSharedAcrossApplications
---

I have read that Foundation and AppKit are loaded into memory once and shared across all running applications. This fact seems like a true statement since simple Cocoa command-line tools don't require that much memory. I am working on a framework that is using global variables. I decided to see the scope global variables (declared within a framework) have across multiple processes that link against the custom framework. It appears that a framework's global variables are initialized for every process (which seems like a stable thing for the OS to do). So does this mean that it is safe to use global variables in frameworks? 

----

In general, the OS will preserve the illusion that your app is the only app running on the entire system, unless you actively do something to find out otherwise. This means that the only parts of an app or library that can be safely shared between apps are either parts that can't be written to (code) or parts that are explicitly asked to be shared (shared memory, etc.). It should be perfectly safe to use globals in a framework.

