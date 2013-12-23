---
layout: page
title: BuildingWithMultipleTargets
---



ProjectBuilder is an amazing development tool - and it's free! Until lately, I have not been able to tap fully into the features of ProjectBuilder and would like to share some of what I learned with you.

----

Have you ever wanted to make a project that built both an application and a framework? Or a framework that also built a test application? Or any combination of Applications/Frameworks/Libraries/Bundles/etc.?

This can be easily done with ProjectBuilder

All you need to do is select the "targets" tab, right click, and select 'create new target' from the contextual menu. Select what kind you want from the sheet that comes up and a new target is made. You can switch between targets using the pull down menu at the top of the main window or from the targets tab.

Using the little checkboxes under 'Files,' you can select which files will be processed for each build target.

But what if you want to make one target and then another?

That is also easy to do. Lets say you have an application and a seperate target to make a framework. Under 'targets,' you can drag the frameworks build target into the applications and it will appear underneath it. This sets up a dependency and ProjectBuilder knows that it has to build the framework and then the Application.

----

That's the basics for multitarget projects. You can also play around with aggregate build targets and completely new build styles, but that is somewhat beyond the scope of the article.

- SamGoldman

