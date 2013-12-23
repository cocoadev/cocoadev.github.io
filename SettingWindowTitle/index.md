---
layout: page
title: SettingWindowTitle
---

see also WindowSetTitle

I'm (very) new to Cocoa, and I'm making a simple document-based app. I'd like to call new windows something more descriptive than "Untitled" - how might I go about doing that?

I've tried adding a button to the NIB with code:
[window setTitle:@"new title goes here"];
and this works fine. (window is an IBOutlet that I've connected to the NSWindow object.)

When I add that code to - (void)awakeFromNib, the title doesn't update. Yes, it is getting called, it just has no effect (and no logged errors, either).

It doesn't work with the - (void)windowControllerDidLoadNib:(NSWindowController *) aController method either.

What is the proper way to set an the title of a document window?

Thanks!

----

You could try overriding     - (NSString *)displayName in your document class, check if     [self fileName] is nil (whether or not the document has been saved) and if it is, return whatever you'd like. Or you could implement     - (NSString *)windowTitleForDocumentDisplayName:(NSString *)displayName in a NSWindowController subclass. You should be aware, though, that the HIG say unsaved documents should be called 'Untitled', not anything else.

[http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSDocument.html]

[http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSWindowController.html]

[http://developer.apple.com/documentation/UserExperience/Conceptual/OSXHIGuidelines/XHIGWindows/chapter_8_section_4.html]

----

Just to clarify on the above, the AHIG says it must be in lowercase. Which the default implementation of     displayName in NSDocument has wrong.

--AllanOdgaard

*see HowToLowerCaseNewDocUntitledTitle for a solution, if this keeps you awake at night*

----

I wasn't expecting such quick responses!

I'll try doing what the second poster said to override the document name.

Actually, these "documents" aren't savable - they display the contents of files fetched from the internet. I assumed that the NSDocument structure would be best for spawning multiple (similar) windows, and it seemed to be working fine.

I'm really liking the efficiency of OOP, I had tinkered with ObjC a few years ago, and found it incredibly annoying (I hadn't grasped the OO concept yet). But I've written a fully-functional web-based application on an hour-long bus ride (thanks to internet via Bluetooth), which would have taken me much longer in most any other language I can think of.

----

If it's working for you, then fine, but if you're not dealing with editing/saving, NSDocument has a lot of overhead you don't really need. You could probably get away with just using a NSWindowController subclass to manage the windows.

