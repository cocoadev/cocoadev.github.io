---
layout: page
title: XCodeTips
---




Add your tips for General/XCode here:

----

**Recent Tips** (applicable to General/XCode 2.3)



*
**Enabling Warnings**
If you'd like to enable as many of gcc's compiler warnings as possible, there are a few pitfalls to be aware of when doing so with Xcode. The following is my advice based on my experience.

First, go to your project or target settings and filter the build settings using the "Warnings" collection.  Now uncheck all the warning options.

As suggested by Apple in this their "Static Analysis" document <http://developer.apple.com/tools/xcode/staticanalysis.html> a good place to start is adding -Wall and -Wextra to the "Other Warning Flags".  gcc provides zillions of warnings, but a great many of the most useful ones are enabled by these two simple flags.

Next you need to add additional warnings one by one.  There are a few ways to do this.

The best choice is to enable a warning using the checkboxes Xcode provides.  Note though that many of these checkboxes are for warnings that are already included by -Wall and -Wextra.

The next best choice is to put the warning in the "Other Warning Flags" setting.  However some warnings only apply to some languages and gcc will give a warning if a warning flag is passed when compiling a file of the wrong language!  So if you have a project with multiple languages (.c, .m, .mm, .cp, etc.) you will get warnings about your warnings! :(

To work around this, C-only warnings need to be added to "Other C Flags" (make sure your "Other C++ Flags" do not include "Other C Flags") and C++-only warnings need to be added to "Other C++ Flags".  There is no "Other Objective-C flags", so Objective-C-only warnings will need to be specified per-file.

One reason to prefer the checkboxes Xcode provides (over adding flags) is that Xcode is then smart enough to only apply language-specific warnings to files of the corresponding language.  For example, prefer the "Effective C++ violations" checkbox over -Weffc++ because the former is smart and is only applied to C++ files, the latter will generate annoying warnings for any .m or .c files in your project.

So now the question is: which warnings are available that are not included by -Wall and -Wextra, which are language-specific, and which do not already have a checkbox in the Xcode GUI?  For that, you need to read through the gcc man page.

I have compiled a fairly comprehensive list, but I'm sure it's not fully complete.  I've organised them by language and included some comments, including the name of the built-in Xcode setting for the warning (ie the ones with corresponding checkboxes):

    

------
C only
------

-Wdiv-by-zero

-Wtraditional
If you care about K&R C

-Wdeclaration-after-statement
If you care about pre-C99

-Wbad-function-cast

-Wstrict-prototypes

-Wold-style-definition

-Wmissing-prototypes

-Wmissing-declarations
GCC_WARN_ABOUT_MISSING_PROTOTYPES = YES

-Wnested-externs

--------
C++ only
--------

-Wabi

-Wctor-dtor-privacy

-Weffc++
GCC_WARN_EFFECTIVE_CPLUSPLUS_VIOLATIONS = YES

-Wstrict-null-sentinel

-Wold-style-cast

-Woverloaded-virtual
GCC_WARN_HIDDEN_VIRTUAL_FUNCTIONS = YES

-Wsign-promo

-Winvalid-offsetof
GCC_WARN_ABOUT_INVALID_OFFSETOF_MACRO = YES

----------------
Objective-C only
----------------

-Wselector
gives very many warnings

-Wstrict-selector-match

-Wundeclared-selector

-------------
All languages
-------------

-Wsystem-headers
Apple's header give too many warnings

-Wfloat-equal

-Wundef

-Wendif-labels

-Wshadow
GCC_WARN_SHADOW = YES

-Wlarger-than-len
you have to pick len, for example: -Wlarger-than-10000

-Wpointer-arith

-Wcast-qual
Useless for Cocoa, will warn for:
General/NSString*	str = @"hello";
<rdar://4625600>

-Wcast-align

-Wwrite-strings

-Wconversion
Useless for Cocoa, will warn for:
[button setEnabled:YES];
warning: passing argument 1 of 'setEnabled:' with different width due to
prototype
<rdar://4625881>: Behaves Correctly

-Waggregate-return

-Wmissing-noreturn

-Wmissing-format-attribute

-Wdeprecated-declarations
GCC_WARN_ABOUT_DEPRECATED_FUNCTIONS = YES

-Wpacked

-Wpadded

-Wredundant-decls

-Wunreachable-code
Will warn on every line where you use assert() from assert.h.
<rdar://4625614>

-Winline

-Winvalid-pch

-Wdisabled-optimization

-Wshorten-64-to-32

-Wformat=2

-Wfour-char-constants
GCC_WARN_FOUR_CHARACTER_CONSTANTS = YES

-Wnewline-eof 
GCC_WARN_ABOUT_MISSING_NEWLINE = YES



For my projects, some of the above warnings are too obnoxious, here are the exact settings I use for my projects' xcconfig files:

    

// C-only warnings
GCC_WARN_ABOUT_MISSING_PROTOTYPES = YES
OTHER_CFLAGS = -Wdiv-by-zero -Wbad-function-cast -Wstrict-prototypes -Wold-style-definition -Wnested-externs

// C++-only warnings
GCC_WARN_HIDDEN_VIRTUAL_FUNCTIONS = YES
GCC_WARN_ABOUT_INVALID_OFFSETOF_MACRO = YES
OTHER_CPLUSPLUSFLAGS = -Wabi -Wctor-dtor-privacy -Wstrict-null-sentinel -Wsign-promo

// Objective-C-only warnings: must be set per file in multi-language projects,
// try adding them to WARNING_CFLAGS and see if you get superfluous warnings
// -Wstrict-selector-match -Wundeclared-selector

// For all languages
GCC_WARN_FOUR_CHARACTER_CONSTANTS = YES
GCC_WARN_ABOUT_MISSING_NEWLINE = YES
GCC_WARN_ABOUT_DEPRECATED_FUNCTIONS = YES
GCC_WARN_SHADOW = YES
WARNING_CFLAGS = -Wall -Wextra -Wundef -Wendif-labels -Wlarger-than-10000 -Wpointer-arith -Wcast-align -Wwrite-strings -Wmissing-noreturn -Wmissing-format-attribute -Wpacked -Wredundant-decls -Winline -Winvalid-pch -Wdisabled-optimization -Wshorten-64-to-32 -Wformat=2



-- smcbride


----

**Recent Tips** (applicable to General/XCode 2.1?)


*By right-clicking in the breakpoints gutter (the thing on the left side of the editor window where you can click to set breakpoints), a contextual menu will appear with a bunch of useful options for debugging. You can "Continue to Here", add breakpoints with actions such as "Log stack trace and auto-continue", and a bunch of other nifty things.


----

**Recent Tips** (applicable to General/XCode 1.5)


* When configuring the toolbar, you can add custom items that contain several of the existing toolbar items. Drag one of those items into your toolbar, then double-click it to specify what items it is to list. The "auto-shuffle" option lets you set it up so the item in the toolbar is always the last one chosen. I kinda like that for a row of debug or run test runs.

* Hold down command and double-click any class name, method, enum, global variable, etc. and Xcode will show you exactly where it's defined. Hold down option and double-click anything and Xcode will start a documentation search for that symbol. These shortcuts are extremely useful and I'm always shocked at how many people don't know about them. -- General/MikeAsh

* If you want a more General/CodeWarrior-esque project window, just double click on the little box with the two stripes and the triangle, above the scrollbar in the middle. The right column will collapse. Drag it once it's in that state to make the window wider/narrower without screwing up its height. *You can still get a General/CodeWarrior-esque window in 1.5, similar to how it was done in General/ProjectBuilder - double-click the vertical split view divider to collapse the files tree, then drag the split view all the way to the right, then resize the window to the left.*

* When auto-completing a method with arguments, use CNTL-/ to skip to the next placeholder.
* Thanks for this.  I've been wondering how to do this for a long time. *

* After having a lots of problem getting General/XCode to correctly build depending projects I found a workaround based on xcodebuild and external targets, see http://machten.blogspot.com for details.

* Use the class browser! Project > Class Browser. It's a window listing either all of your classes, or your classes + framework classes (you choose), with a pane listing their methods, and, optionally instance variables. There's also an embedded editor. I recently discovered it, and it's one of General/XCode's best features, IMO. I wish it had search though.


----

**Old Tips** (not applicable to General/XCode 1.5)


* Resist the temptation to use the groups drawer attached to editor windows, as it will drive you mad.
*I use it all the time... what's the problem?*  *This doesn't even exist in Xcode in 1.5.  Kind of a useless feature anyway... -General/RossDude*  (Well, apparently not if you 'use it all the time' ... ;-)

* I'm not sure if anyone else had this problem, but if the little groups thing on the documentation window starts shrinking, and the resizer doohickey disappears, here's what you do.  Go into ~/Library/Preferences/com.apple.Xcode.plist.  After that, delete the keys General/PBXHelpWindowContentConfiguration and General/PBXHelpWindowGeometry, and all will be well.  It got to the point with me that you couldn't even see the little books anymore! *Great!! Thanks so much!! I had stopped using the doc because of that...* Glad I could help. --General/RossDude

    defaults delete com.apple.Xcode General/PBXHelpWindowContentConfiguration

    defaults delete com.apple.Xcode General/PBXHelpWindowGeometry

-- note that you'll need to restart General/XCode to see the change

-- this is apparently fixed in 1.2: * The Groups pane in the documentation window no longer disappears.* (Release Notes)

* Never turn on predictive compilation.  Sometimes it will freeze Xcode when you build your product.  Then you have to force quit Xcode and open your project up again. --General/RossDude

-- this is fixed in 1.2 -- General/CatfishMan



----

See also:  General/ProjectBuilderTips

----

Watch out! Xcode HATES spaces! Don't have your project or framework in a path that contains a space! From Apple's release notes:
    
Any build setting that contains a path (or includes another build setting that resolves to a path) must be enclosed in double quotes if the path is likely to contain a space character. Xcode will handle the quoted value correctly, but will fail if an unquoted value resolves to a path that includes a space.

