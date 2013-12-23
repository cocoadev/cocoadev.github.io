---
layout: page
title: BackgroundApplication
---

Hello

I need to write an application that sits in the background and performs a task given input from another application. I am however having some difficulties deciding how to build this application. My first thought was a kext, but thats in the kernel and the kernel is really not needed. Thats where cocoa comes in. I then thought of doing a background cocoa application, but the issue I have with this is that the application should start as soon as the system boots and require nothing by the user. This then lead me to look at launchd daemons, but I have an issue of properly communicating with the launchd daemons. I want other programs to be able to modify how the background program behaves.

So my question to the Cocoa crowd is, should I do a straight Cocoa background application? If not why not, and what would you recommend. My requirements are listed below:

- Application starts on boot (prior to login potentially)

- Has a run loop that polls for data periodically -- sleeps rest of the time (polling bad I know, but it works for now)

- Allow for communication between itself and other applications (perhaps through notifications).


I am new to Mac OS X programming and I am simply not sure how to proceed with this and what type of application I should develop to carry out a periodic background task that requires to communicate its results to any apps that want to listen and receive information from any applications that want to alter its behavior. As I am polling is it considered ok to send out global notifications (even if no one is listening) every second? Is there a better method.

Thanks for any advice or guidance

----

Why does your app need to launch before the user logins in? That makes things much, much more difficult. 

Why not just write an agent?:

http://developer.apple.com/technotes/tn2005/tn2083.html#SECAGENTS

----

Hey to my knowledge, the only difference between an agent and a daemon is that the agent allows some basic GUI stuff. In that case it would be better to go with a Daemon I think as I don't need a GUI. My application is trying to grab information on the light sensor and the SMS. I want to send out a message to everyone who registers every minute. So in this case I want it to run before someone logs in so any background apps can access this data. I can head the daemon route, but I am then stuck as to how to best register and relay the information to apps that want it. I was thinking they could register for a NSDistributedNotification, but this is expensive I think.... 
Based upon this utility I plan to develop some apps for personal use and some other things and would like a nice little .. daemon or background app that handles the dirty work for me.

Any ideas? I think I have the communication problem whether I go cocoa, daemon, or anything. Is there no better way to transmit data?
Thanks

EDIT: I guess in hindsight...  a second question is.. what is a fast efficient way for applications to communicate with another application periodically (say every second) for updates. Should I use the NSDistrubutedNotifcationCenter, or should I implement my own observer like mechanism?

----

You may find NSDistributedNotification fails to work properly since it uses mach ports and if you writing a daemon that'll be a problem. The tech note above covers the communications issues you'll face if you writing a daemon (in contrast to an agent). If you do write a deamon just use a UNIX domain socket to talk to your user land apps. Again, read the technote above for all the ugly details.



---- 


Thanks, for some odd reason I thought that entire article was the small blurb you linked to. I just clued in that its much more detailed. I shall give it a read.

