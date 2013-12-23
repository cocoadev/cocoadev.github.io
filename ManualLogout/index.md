---
layout: page
title: ManualLogout
---

Anybody know how to tell OS X to logout a user logged in the GUI?
instead of say the auto-logout feature, i want to have my own program/script that logs them out  based on a duration fetched from a reservation system.

any ideas? haven't been able to find anything so far, maybe looking for the wrong things.
 --JeremyK

----

By logout, do you mean killing the GUI and taking the user to single user mode?

----

I think he means sending you back to the login window.

----

Killing login window does it, but its rather brute force. :)

----

Killing loginWindow does it for the currently logged in user.  However, you can't logout another user by doing that in 10.3 (which is annoying).  In fact, if you kill all of their processes, they are still "logged in."

----
Yeah, i want the user to be 1st regular logged out when time is up, but if they somehow stop it, after a minute or so it'd do a force logout.
Fast user switching will not be enabled in this situation. 

Just got some applescripts that will do both types of logout, yay
-- JeremyK

----

Mind sharing with us? :)
----
    
try
	-- graceful logout
	tell application "loginwindow" to �event aevtlogo�
on error mes
	display dialog "Error: " & mes giving up after 10
end try

Supposed to be hard logout, apparently doesn't and i might be resorting to kill -15 loginwindow of the particular user
    
try
	ignoring application responses
		tell application "loginwindow" to �event aevtrlgo�
	end ignoring
on error mes
	display dialog "Error: " & mes giving up after 10
end try

