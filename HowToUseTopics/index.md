---
layout: page
title: HowToUseTopics
---

**The Basics**----

Topics are automatically generated lists of other pages; they can be easily created by putting \\[Topic\\] (or \\[Topic_chron\\] for a chronologically-sorted list like on CocoaDiscussions) on a suitably-named page; let us suppose we are creating StarWarsFans. First, set up that page with something like:

    Here is a list of all Star Wars fans on this site: \\[Topic\\]

To add a page to StarWarsFans, one merely puts 
    \\%\\%BEGINENTRY\\%\\%StarWarsFans[: section][- comment]\\%\\%ENDENTRY\\%\\%
somewhere on the page being added, with optional section (caps- and whitespace-sensitive) within the topic (not supported on chronologically-sorted listings) and optional comment. No returns are allowed in this entry, but standard formatting commands are allowed in comments.

For example, we might add KritTer by putting     \\%\\%BEGINENTRY\\%\\%StarWarsFans:Lukewarm - Only seen episodes I and VI\\%\\%ENDENTRY\\%\\% to his page. KritTer will show up in our new topic, as     Here is a list of all Star Wars fans on this site:* Lukewarm* KritTer - Only seen episodes I and VI

Because new pages cannot be created in topics by editing the page, it is helpful to put \\[New\\] on frequently-added-to topic pages for speedy page creation.

A nice feature you may want to take advantage of is that \\[Topic\\] will not expand inside an entry, it will expand on the topic page that entry is for. Check out MemoryManagement, for example.

----**Some general rules**----

Topics are there to be used. If you start a discussion on a page, add it to CocoaDiscussions; in six months time when everything has been said, someone can find the page and re-factor it to make the information easily accessible. This applies no matter what page you start a discussion on, even if it is a long-standing page. This is primarily an information site, so it's important we remove stale old conversations and keep the nuggets of useful information.

Try to distinguish between major topics and minor ones. A major topic is something like CocoaDevUsers; lots of pages only tenuously connected in subject matter link to it. These should be scarce in number, as every one either fattens the CocoaDev front page or puts information further from the user. Sections within a topic should be favored over splitting a major topic in two, for instance, and discussions should be refactored/retired rather than creating separate discussions pages.

A minor topic is something like DesignCollection; a group of pages fleshing out a major page. These are a good way of breaking up a long page, or of grouping highly related pages; feel free to create these willy-nilly.

Lying midway between major and minor topics are subject topics.

----**Subject topics**----

One of the best uses of topics is to join together many pages on some large but important subject in Cocoa programming. See for example MemoryManagement and ThreadSafety. If you notice such a theme running across many pages, do go ahead and make a subject topic about it. These only serve to make information easier to find, hopefully improving the scalability and utility of this site. Broad topics like CocoaGlossary and AppKit will only get more unwieldy as this site grows!

