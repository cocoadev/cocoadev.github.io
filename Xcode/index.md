---
layout: page
title: Xcode
---


Xcode is Apple's IDE for making OS X and iOS software.

Xcode is primarily built around using Objective-C, although it also includes first-class support for C, C++, and Objective-C++. You can also use it to edit source files in numerous other languages, including Java, Python, and Ruby, and non-source files such as plists and xibs.

Many older books were written for Xcode 3 and the accompanying but separate General/InterfaceBuilder application. You should not attempt to use these with Xcode 4, no matter how good anyone tells you such a book was. The differences are too great.

== Key features ==

* Toolchain for turning source code into OS X or iOS apps
* General/InterfaceBuilder for graphically designing graphical user interfaces
* Integrated debugging environment
* General/Instruments for profiling of usage of memory, processor(s), long-term storage, and more

== Workflow optimization ==

=== The lay of the land ===

[TODO: Screenshot]

#Navigator
#Jump Bar
#Inspector
#Library
#Editor (main)—may contain any kind of file: project, source, interface, plist, etc.
#Editors (assistant)
#Debugger

=== Navigation ===

First, we must take a moment to understand what “navigation” means—it has a very specific (and yet very broad) meaning in Xcode.

“Navigation” is any action that causes a file to be loaded into an editor, or if it is already loaded, any action that may change the selection within that editor (e.g., navigating to a build error). This includes:

*Clicking a file in the Project Navigator
*Clicking an issue in the Issues Navigator
*Clicking a search result in the Find Navigator
*Choosing anything in an editor's Jump Bar
*Pressing OK (including Return/Enter) in the Open Quickly panel

=== Tabs ===

It may not be immediately obvious, but Xcode 4 supports multiple tabs within each workspace/project window.

You absolutely should familiarize yourself with tabs. They take Xcode 4's General/UserExperience|UX from painful to workable.

⌘T creates a new tab that is a clone of your current tab, but as we'll see, there are better ways to create tabs most of the time.

Each tab defaults to showing the name of the file in its main editor, but you can set a specific name for the tab by double-clicking on it. This becomes important for setting up behaviors (described below).

=== Optional navigation ===

Recall the definition of “navigation” above.

The first thing you should do in Xcode is, in its Preferences, set the “Optional Navigation” to “Uses Separate Tab”. “Optional Navigation” is navigating with the Option key held down. This includes all of the navigation actions listed above, from Option-clicking a file in the Project Navigator to holding down Option while pressing OK in the Open Quickly panel.

After you've set optional navigation to “Uses Separate Tab”, all of the above will perform that navigation into a new tab.

(Xcode is smart enough to reuse a tab if you so navigate from some other file. So, for example, if you're in Foo.m and optionally-navigate to somewhere in Bar.m, and you have a tab for Bar.m already, Xcode will activate the existing Bar.m tab. On the other hand, if you're in Bar.m already and optionally-navigate to something else in the same file, Xcode will always create a new tab.)

=== Behaviors ===

While these may seem to be a power-user feature, they're actually essential to bending Xcode to how you think it should work. (Or, to look at it another way: All programmers are power-users, so you should get used to using power-user features in your tools.)

Behaviors enable you to tell Xcode what you want it to do in each of a variety of circumstances. Available triggers include build failures, a program being debugged emitting output, a program being debugged abnormally terminating, and so on. Available actions include activating a specific tab, showing a specific Navigator, showing a specific Inspector, showing the Debugger pane, and so on.

=== The power of shift-option ===

The shift-option modifier combination applies to every navigation action, just as option does in “optional navigation”.

You choose what optional navigation does in advance, but you choose what shift-option does at the time that you do it. Shift-option navigation brings this up:

[TODO: Screenshot]

In this popover, you choose exactly where the navigation should go to:

*An existing editor in the same tab
*A new assistant editor in the same tab
*The main editor in another existing tab
*A new tab
*A new window

As with optional navigation, **all** of the navigation actions listed above can be augmented with shift-option. Try them yourself!

=== Tying it all together: The two workflows ===

There are basically two ways to use Xcode 4.

#**Open one tab for each file.** When you want to work on a file you don't currently have open, Option-navigate to it. When you're done working on a file, close its tab.
#**Open one tab for each task.** Have one tab for project management, one for source editing, one for interface editing, one for build logs, and one for debugging. Use Behaviors to automatically switch tabs when needed (e.g., when a build fails or the program crashes).

== History ==

Xcode replaced an older IDE called General/ProjectBuilder, which dated back into the NeXT days. (Xcode 3.x was clearly descended from it.)

Xcode 3 and older used the GNU Compiler Collection (GCC) for the build toolchain, and the GNU Debugger (GDB) for the debugger. As noted above, these have since been replaced.

Some Xcode features have come and gone. For example:

* General/FixAndContinue, which let you make a change to one file, recompile it, and load it into the running executable, WITHOUT quitting, relinking, and relaunching the whole thing… when it worked.
* General/ZeroLink, used only during development to speed up compiling by skipping the linking step and instead having the executable load the .o files as needed. Many developers accidentally left this on in Release builds and shipped non-functioning executables to testers and/or end users. In the end, the time savings was negative.

=== Xcode 4 ===

As of 4.x, General/InterfaceBuilder is part of Xcode; it's the nib/xib and storyboard editors.

Xcode 4 uses Apple's own Clang compiler, and introduced a related Low-Level Debugger (LLDB).

== External links ==

*https://developer.apple.com/technologies/tools/
