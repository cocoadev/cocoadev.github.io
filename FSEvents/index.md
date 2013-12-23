---
layout: page
title: FSEvents
---

FSEvents is supposed to be this really cool framework allowing you to get notifications when a specific FS event occurs.  SOOO much better than kqueue which was super lame.  Only one thing.  It's MIA.  The docs say it should be in /System/Library/Frameworks/FSEvents.framework.  Sample code links to it but... it's not there.  Does anybody know where it went?

----
Is it not part of CoreServices?

Meanwhile, FSEvents is a little disapointing IMHO - it is not fine grained fsevents (i.e the low-level non-public API) - it basically only informs of directory changes and volume mount/unmount, and whilst it does maintain logs, these logs have no time information beyond that of the ordering implied by the event id.

Sample code of basic usage at http://www.fernlightning.com/doku.php?id=software:fseventer:leopard 

----
"It" is indeed part of CoreServices... only thing is the docs (see: file:///Developer/Documentation/DocSets/com.apple.ADC_Reference_Library.CoreReference.docset/Contents/Resources/Documents/documentation/Darwin/Reference/FSEvents_Ref/index.html ) tell a different story... I was hoping there'd be something in the headers that's not in the docs....

----

*it basically only informs of directory changes and volume mount/unmount, and whilst it does maintain logs, these logs have no time information*...

The excellent Ars Technica review (http://arstechnica.com/reviews/os/mac-os-x-10-5.ars/7 ) explains the rationale behind the public FSEvents framework. In a nutshell, because it must record the entire history of events for those apps that are not running all the time, to keep the log size manageable it operates at the directory rather than file level. Once you know a directory of interest has changed you can scan it yourself. Events are given a unique 64-bit ID rather than a timestamp (due to automatic coalescing of events that occur within 30 seconds of each other to save space) but you can convert this to an approximate time.

----
SCEvents - FSEvents Objective-C wrapper: http://stuconnolly.com/blog/fsevents-objectivec-wrapper/ -- StuartConnolly

