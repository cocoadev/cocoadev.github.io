---
layout: page
title: SubFrameworksInXCode
---

.

Defining a proper, logical structure to a new project is an important early step. It will save time later, when you realise you really need to enforce a bit of order, and will make development easier over the life span of the project.

One useful structure that can be fairly well managed in General/XCode makes use of frameworks to contain the bulk of your project's code. A framework can be created as a 'sub-project', and a build step added to your main target that builds the framework before continuing. This can aid in enhancing the readability and maintainability of your code, and helps to encourage and enforce the use of the model-view-controller paradigm.

Another benefit of using this technique is that at a later point, others can make use of the framework.

An example of where this will come in handy is VLC. A Google Summer of Code project has been started this year to move the bulk of VLC's code into a framework, where it can be used by other applications. I'm looking forward to the result of this project, as it will enable integration of VLC's excellent playback engine into other software, such as media center applications like iTheater. 
The same goes for many other types of applications - a news reader can be integrated into a screensaver, or an IRC client into a video game. Frameworks are a very nice way to share functionality.

Firstly, this is fairly well documented - the magic words are 'embedded framework'. Google them for more info, or read the Apple documentation at http://developer.apple.com/documentation/General/MacOSX/Conceptual/General/BPFrameworks/Tasks/General/CreatingFrameworks.html.

There are two ways to use embedded frameworks in General/XCode. You can either have the framework in the same project as the main application, or use a separate project embedded in the main project. Using a sub-project can be convenient, as it can clean up the main project. If yours is an open source application, you can also then distribute the framework's project separately. Having the framework in the same project as the main application can also be convenient, as the same project window allows access to both the framework and the main app. It's a matter of preference.

**
Separate-project frameworks
**

To use sub-frameworks in an General/XCode project, there are a few steps that need to be taken.


* Create/load your main project
* Create a new Cocoa Framework project (File - New Project - Cocoa Framework - Next), call it something like M<nowiki/>yApplicationFramework, and store it under your main project (Enter M<nowiki/>yApplicationFramework in 'Project Name', then click 'Choose...' and select your project folder)
* In the new project, bring up the properties for the main target (Right click on the M<nowiki/>yApplicationFramework target under Targets in Groups & Settings, and click Get Info). In the 'General' tab, change 'Name' to 'M<nowiki/>yApplication'. Under the 'Build' tab, scroll down to the 'Product Name' setting, and change it to 'M<nowiki/>yApplication'. Close the dialog.
* Add an aggregate target called 'Build and Install' (Right click on 'Targets', click 'Add - New Target', select 'Aggregate' and click 'Next', enter 'Build and Install' and click 'Finish')
* Add 'M<nowiki/>yApplication' as a direct dependency (In the 'Build and Install' info dialog that appears, click the plus at bottom-left, and select 'M<nowiki/>yApplication', then 'Add Target'). Close the dialog.
* Add two copy files build phases: One to '/Library/Frameworks' (absolute path), and one to '../../../build/$(CONFIGURATION)' (products directory). (Right-click on 'Build and Install', select 'Add - New Build Phase - New Copy Files Build Phase'; select 'Absolute Path' in Destination, then enter '/Library/Frameworks' in 'Full Path'. Close the dialog, then repeat the 'Add - New Build Phase - New Copy Files Build Phase' process; select 'Products Directory' in Destination, then enter '../../../build/$(CONFIGURATION)' in Path. Close the dialog)
* From the 'Products' group, drag M<nowiki/>yApplication.framework into both 'Copy Files' build phases (two drag operations)
* Back in your main project, create a new group called 'Core Framework' (Right-click on the project root under 'Groups & Files', select 'Add- New Group', and enter 'Core Framework')
* Drag the project root from the framework project's 'Groups & Files' list into the 'Core Framework' group you just created under the main project. Click 'Add'
* Bring up the properties dialog for the main target (Right-click the 'M<nowiki/>yApplication' target under 'Targets' and click 'Get Info'), then add the 'Build and Install' target from the framework as a dependency (Click the plus button at the bottom left, and select 'Build and Install')
* Click the 'Build' tab, and scroll down to the 'Other Linker Flags' setting; add '-framework M<nowiki/>yApplication'. Close the dialog.
* Assuming you want to include the framework in your application bundle, add a Copy Files build phase to the main target, with destination 'Frameworks' (Right-click the 'M<nowiki/>yApplication' target, select 'Add - New Build Phase - New Copy Files Build Phase', select 'Frameworks' in 'Destination', and close the dialog); From the 'M<nowiki/>yApplicationFramework.xcodeproj' item under the 'Core Framework' group, drag 'M<nowiki/>yApplication.framework' into the new Copy Files build phase.


If you've set it up correctly, your Groups & Files panes for the main project and framework project should resemble the following:

http://michael.tyson.id.au/uploads/200705151446-2.jpg
http://michael.tyson.id.au/uploads/200705151446-1.jpg
 
Now the bulk of your application's code should be written within the framework, and compiled in to the main project, which should contain the interface-specific code to put it all together. Import headers from the framework using     #import <M<nowiki/>yApplication/M<nowiki/>yApplicationHeader.h>

An important step when creating header files within the framework is to declare their roles as 'public' if you wish them to be accessible outside the framework (in your main project, for example). This can be done by selecting the 'Copy Headers' build phase of the main target for the framework. In the corresponding file list, each header is displayed. Under the 'Role' column, each header's role is displayed - the default role is 'Project', which indicates to the build process that the headers are not to be copied into the resulting framework. Change the role to 'Public' to include the headers within the framework bundle.

(Thanks to the iTheater team, whose excellent project structure I examined to learn how to do this)

**Single project frameworks**

If you wish to use just one project for both the framework and the application, perform the following steps (these are derived from the apple documentation page linked at the top of this entry):


* Once you have created/opened your project, create a new build target (Right-click on 'Targets' under 'Groups & Files', and click 'Add - New Target'.). Select 'Framework' under 'Cocoa', and press Next. Call the target something like 'M<nowiki/>yApplicationCore', and click Finish.
* Click the 'Build' tab, scroll down to 'Installation Directory', and change it to '@executable_path/../Frameworks'. Close the dialog.
* Open the main application target under 'Targets', and from the 'Products' group, drag the M<nowiki/>yApplicationCore.framework product into the 'Link Binary With Libraries' build phase.
* Add a build phase to the target (Right-click the 'M<nowiki/>yApplication' target, select 'Add - New Build Phase - New Copy Files Build Phase'), with 'Destination' set to 'Frameworks'.
* Drag the M<nowiki/>yApplicationCore.framework product into the new build phase
In the information dialog for the main target (Right-click on the target, select 'Get Info'), add the framework as a dependency (Click the plus button at bottom left, and select M<nowiki/>yApplicationCore.framework)
* Source files can then be added to either the main application or the framework by selecting the checkbox beside the desired target when adding new files. To configure existing files for certain targets, right-click on the selected source files, click the 'Targets' tab, and select a target. You will want to add at least one source file to the framework before test-building.


If all went well, your 'Groups & Files' pane should look like this:

http://michael.tyson.id.au/uploads/200705151627.jpg

Import header files from the framework with     #import <M<nowiki/>yApplicationCore/M<nowiki/>yApplicationHeader.h>

(This tutorial originally posted at http://michael.tyson.id.au/archives/415-Creating-applications-in-General/XCode-using-frameworks.html.  Thanks to the iTheater team, whose excellent project structure I examined to learn how to do this)
