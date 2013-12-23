---
layout: page
title: GettingAListOfApplicationsThatOpenACertainFileType
---

Hi,

I am looking for a way of finding a list of applications that will open a certain data type. For instance, if i have an image, and I want to give the users the ability to pass it off and open it in another image app like photoshop or preview, how do I get a viable list of those applications? I have searched for a while in the cocoa API's and I can't find anything yet. Please Help!

----

"To find all known applications that can open a given item with a specified role, use the Launch Services function LSCopyApplicationURLsForURL. Although this function can only accept a URL reference and not a file-system reference, you can use it for document files as well, by passing a URL with scheme file referring to the desired document."

http://developer.apple.com/documentation/Carbon/Conceptual/LaunchServicesConcepts/LSCTasks/chapter_3_section_3.html#//apple_ref/doc/uid/TP30000999-CH203-DontLinkElementID_15

