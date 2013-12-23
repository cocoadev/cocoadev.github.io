---
layout: page
title: CheckedMenuItemsForDocumentBasedApp
---



Hi -- I would like to have a document based application with a menu that has several mutually-exclusive options in it.  Selecting one of these menu items should cause a checkmark to be placed next to it and any previous checkmark cleared.

I can already do all this; I've sent the menu actions to the First Responder, and those messages happily make their way to my NSWindowController subclass.

What I can't seem to figure out is how to get a message when the frontmost document changes, so I can update the checkmarks to reflect the state of the particular document.

As a concrete example, imagine you were writing a document-based image application, and you had a menu with two choices: "Show Unaltered" and "Show Inverted".  You want a checkmark next to the menu item that reflects the state of the frontmost document.

How would you go about this?  I suspect there's just some message one of my classes will get when the frontmost window changes, but I can't find that anywhere.


----
First, you only need to update the check marks right before the menu is about to be displayed.  I recommend that you do your updating in - (BOOL)validateMenuItem:(id <NSMenuItem>)menuItem which is called automatically right before a menu is item is displayed.
http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSMenuValidation_Protocol/Reference/Reference.html

Second, you should implement -validateMenuItem: in some object that is in the responder chain so that the correct object is always asked to validate menus based on the responder chain.  Your use of NSWindowController for this purpose is probably fine.  Just implement -validateMenuItem: in you NSWindowController sub-class and use [self document] to get the associated document.  Better still, just implement -validateMenuItem: in you NSDocument subclass and then the document that owns the key window will always be the one that validates the menu items.

Third, you clearly need to read or re-read about the responder chain and work on some tutorials: 
http://developer.apple.com/documentation/Cocoa/Conceptual/EventOverview/EventArchitecture/chapter_2_section_5.html
http://developer.apple.com/documentation/Cocoa/Conceptual/EventOverview/EventArchitecture/chapter_2_section_6.html
http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CoreAppArchitecture/chapter_7_section_6.html

Fourth, please don't use check marks in menus in any application I might have to use.  Check marks do not indicate mutually exclusive options; they indicate any selection of non-exclusive options.  You may want to use radio buttons somewhere else in the application.  Menu items are not suited for the use you propose, and the bastard craptasic behavior you want is reminiscent of Windows or Mac OS 9.  There is no user interface convention that informs users how or when check mark menu items should be used, and there is no indication that some options may be mutually exclusive either.

----
A better choice here would probably be a *single* menu item whose name changes between "Show Inverted" and "Show Unaltered" depending on the state of the document. See the top four menu items under Safari's View menu for an example of how that would work. You can use the same validateMenuItem: trick to update the name to match your document's state on demand.

----

Sadly, check marks as indicators for mutually exclusive options in a menu is in fact a normal thing in OS X; open a couple of documents in TextEdit and look at the contents of the Window menu. (Same goes for multiple windows in Preview, Safari, etc.) -- RobRix
----
The Window menu is special; it also has a diamond glyph for minimized windows.
----
It's not special in this context-- it's a design decision by someone who thought they could get away with using a check mark to indicate a mutually exclusive selection.

The question of whether you want to make the same decision or not is up to you either way, you've just got some precedent if you want to use it. (Personally, I don't like it at all.)

-- RobRix
----
Many, many applications use "radio menus" outside of the Window menu: off the top of my head, the Finder, Mail, System Preferences, and iTunes. (Examples of third-party apps include everything from Adium to VLC.) Even the HIG (lol) encourages it: "*You can use checkmarks for mutually exclusive attribute groups (the user can select only one item in the group, such as font size)*." http://developer.apple.com/documentation/UserExperience/Conceptual/OSXHIGuidelines/XHIGMenus/chapter_16_section_3.html

In fact, it's *more* clear to use two menu items than one that changes its label, because it shows what the alternate state is. Obviously in the case of turning something on or off, two menu items would be unnecessary, just like using two radio buttons over a checkbox. But for more complex things (e.g. writing direction), having both options provided can be helpful. It also means that each item can be a noun instead of a verb, which is useful.

As for the actual question, either use     -validateMenuItem: as recommended or watch for when your window becomes/resigns main.

