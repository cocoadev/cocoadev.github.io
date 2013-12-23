---
layout: page
title: StoringUsersLoginInfo
---

My app needs to authenticate the user frequently to run a task that requires root priveleges (specifically, pmset). Would it be terrible to store the user's account login and password in the Keychain? Is there any good way of making it so the user doesn't have to type their password every time? Thanks in advance.

----

I suggest searching the Apple documentation for "Security" ... this will link you to the various manuals for you to read. Once you read them, you'll see at least several reasons why it would indeed be "terrible" to do so. Authentication is **NOT** to be short-circuited. If I'd ever find out an application on my system tried to do such a thing, I'd run it up the news flagpoles faster than anything and you can be sure nobody in their right minds would use your application.

----
Okay, thanks.
