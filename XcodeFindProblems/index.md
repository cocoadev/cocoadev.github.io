---
layout: page
title: XcodeFindProblems
---

I just installed Xcode v1.2 and I can no longer do text searches. Xcode's find progress indicator just spins and no search results get posted. Anyone else experience this?

----

OK, here's what I figured out. I am only having problems searching text within a particular project. This project has many targets (12+) and many nibs. For some reason I added a bundle to the Group&Files tree (a resource with .bundle extension) that had something wrong with it. What exactly was wrong with the bundle is beyond me, but once I figured out that this resource was causing Xcode to crash I did the following.


*moved the problem bundle (with the Finder) from the project folder to a temporary location
*launched Xcode and openned the project again
*deleted the reference to the bundle in the Groups&Files tree


This solved my problem with not being able to search text within this particular project. If anyone knows how a corrupt bundle can cause this type of problem, please share your knowledge.
