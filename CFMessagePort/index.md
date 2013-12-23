---
layout: page
title: CFMessagePort
---

See: http://developer.apple.com/techpubs/macosx/Essentials/SystemOverview/InverEnvironissues/chapter_14_section_9.html

----
The link above is dead, but info available here: http://developer.apple.com/documentation/CoreFoundation/Reference/CFMessagePortRef/Reference/reference.html

Note: CFMessagePortSendRequest leaks memory if you pass NULL for replyMode (10.4.3 and later?). Pass kCFRunLoopDefaultMode instead. 

----

Has this be logged in Radar? Is it fixed on 10.5?

