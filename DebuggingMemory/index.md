---
layout: page
title: DebuggingMemory
---



Is there a way to have Xcode display memory as a block of bytes when debugging? I need to see the changes to a block of memory as I step through code, and Xcode's variable window simply doesn't cut it for this.

Ideally, something that shows the hex values for memory and the byte values, and also the bytes themselves displayed as characters (much like a hex file editor/viewer).

Any way to do this? -Seb
----
I just checked Xcode help, and it turns out you can...sort of. I'll leave you to figure out the specifics. While we're on the subject, is there any way to view the contents of an NSDictionary or an NSArray? --JediKnil  **Before I helplessly look through the docs, which version of Xcode specifically are you talking about?** Thanks for reminding me. I meant Xcode 2.1, but info for any version should probably be posted here anyway. *1.5 has this too (Debug>Tools menu), but not the variable specific option described below. It works as described above, but it doesn't update dynamically (at least on 1.5); you have to "refresh" it to update the display*

*From Apple's docs: When execution of the current program is paused in the debugger, you can browse the contents of memory using the memory browser. To open the memory browser, shown here, choose Debug > Tools > Memory Browser. You can also open the Memory Browser to the location of a particular variable; in the Debugger window, select the variable and choose Debug > Variables View > View As Memory.*

Never mind! Reading an array is actually really easy: you can log any object by clicking on it and choosing Debug > Variables View > Print Description to Console (or by Control-clicking the item) --JediKnil
----

OK, here's a another one: is it possible to 'watch' a variable (where the debugger stops on a variable value change); I've searched the docs but found nothing. Please also keep in mind I'm still on Xcode 1.5 -Seb

----

gdb supports watchpoints.  Drop into the gdb console and do 'help watch'.  It's pretty slow because the PPC (or maybe OS X) doesn't support fine-grained ranges for watching memory changes.  Right now it marks the whole page as read-only and catches the page fault.

*How long does it take to do its thing? Whenever I continue after setting a watch on a variable, nothing happens and it just seems like the program is frozen.*

