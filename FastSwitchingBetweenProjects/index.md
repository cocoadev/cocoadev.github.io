---
layout: page
title: FastSwitchingBetweenProjects
---



Taken from EmbeddingFrameworksInApplications

http://rentzsch.com/cocoa/embeddedFrameworks - *is a nice QuickTime howto. The only thing that wasn't clearly explained was how to drag one projects icon into another project. I wasn't aware of this feature so I jumped at the chance to make these interproject dependencies active in a couple of my applications. I got stuck trying to get the project icon to appear in the window's title bar (if you watch the movie you'll know what I'm talking about). Anyways, to get the project icon to appear in the project's main window title bar, just select the project item at the top of the groups and files pane, toggle from the embedded editor (command + shift + 'E') to the project's file info view (make sure none of the editor is visible) and then click on the view. You should see the title bar go from displaying the name of the current source file being edited to just showing an XCode icon with the name of the project.*

Basically, if you watch the QuickTime movie you will be able to create project references inside other projects. Once you have an icon of an external project inside the Groups&Files pane, you can double click on this icon to either open the external project or to make this external project's main window key.

