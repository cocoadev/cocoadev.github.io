---
layout: page
title: GetSearchTermsOnFileOpen
---

Some applications (e.g. OmniOutliner) will highlight text when you perform a Spotlight search and open a particular file.

e.g. Performing a Spotlight search for "chocolate" and opening a matching OmniOutliner document will result in "chocolate" appearing in the search box. The result is the document shows you where the terms you were looking for exist.

The way to achieve this functionality is through Apple Events, as described under the "Open" section of the following page:

http://developer.apple.com/documentation/Cocoa/Conceptual/ScriptableCocoaApplications/SApps_handle_AEs/chapter_11_section_3.html#//apple_ref/doc/uid/20001239-1119823

The code to achieve this (in case Apple decides to remove that documentation) should go into the application:openFiles: method in your NSApplication delegate:

    
NSString    *searchString = [[[[NSAppleEventManager sharedAppleEventManager]
                                                            currentAppleEvent]
                                                            paramDescriptorForKeyword:keyAESearchText]
                                                            stringValue];


--JeremyHiggs

