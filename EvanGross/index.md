---
layout: page
title: EvanGross
---


----

Long-time Macintosh software developer (since 1984!).

Most recent product release (a Cocoa app, of course):

Spell Catcher X 10.3, by Rainmaker Research Inc. http://www.rainmakerinc.com/

http://www.rainmakerinc.com/images/external/spellcatcherlogo.jpg

----


**Features and tools for developers:**
*
SpellCatcherX 10.3's new DirectCorrect feature has tools to help developers with their text-related accessibility and NSTextInput / TSM CarbonEvent implementations. 
*
See more about these tools at http://www.rainmakerinc.com/developer/



**Features for regular folks:**
*
Global writing support--in virtually every Mac OS X app.
*
Spell checking and Thesaurus look ups in up to 15 languages, including Russian via a Cyrillic InputMode.
*
Shorthand glossary (aka sinppets, autotext, autoreplace).
*
Spelling, shorthand, and most other features available as-you-type and after-the-fact (for existing text).
*
Uses NSSpellServer to integrate with the Mac OS X Spelling Panel.
*
Spell Catcher's commands are available from the Services menu. Takes advantage of Mac OS X's dynamic AddOnServices support, making it possible to change the services provided on-the-fly - including the languages available from Spell Catcher's NSSpellServer.
*
Spell Catcher's commands are also available from its Dock menu and from the InputMenu, for "Services-challenged" applications.
*
As-you-type features are implemented using an InputMethod, a (necessarily so) Carbon component that appears in the InputMenu. Its UI is provided by a (LSUIElement) Cocoa background-only application, communicating with it using CFMessagePort. The UI Server in turn communicates with the SpellCatcherX application using DistributedObjects.
*
Uses NSLayoutManager's temporary attributes to underline errors in a color corresponding to their type, and tag the language of individual paragraphs.


----

Sorry to sound like a product ad, but I've tried to reduce the marketing spin and increase the Cocoa-related coolness.

Evan Gross, Rainmaker Research Inc.

