---
layout: page
title: NSPersistentDocumentDoNotLoadWindow
---

Hi, is there any way to not load the main window in NSPersistentDocument? I created a Core Data Document based Application with Spotlight Importer and I do not want the window that is connected to the MyDocument class to open as a new document. I want to open a panel that gives the user options when they open the app. Like the new iMovie HD and iDVD 5 way. When you launch those apps you get a panel with options to create a new project etc. Thanks in advance for the help!
----
In your M<nowiki/>ainMenu.nib file, create a new object and connect it to your application's delegate outlet. Then implement     applicationShouldOpenUntitledFile: to return     NO (RTM, no offense). Finally, add a window to M<nowiki/>ainMenu.nib that has your options. This should work...good luck! --JediKnil

