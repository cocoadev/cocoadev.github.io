---
layout: page
title: POPThreeHelp
---

Hey, y'all!

I'm trying to make a mail client, but I cannot get it to work.  I have tried with several different socket-classes (SmallSockets, NetSocket..) but what happens is all the time the same:


*    1.  I connct to the POP3 server on port 110
*    2.  I get a reply from the server saying like: "+OK POP3 welcome bla bla bla"
*    3.  Then I send my USER command: "USER *****@******'.com"
*    4.  I sit around waiting for the server to reply with an "+OK yadda bla bla bla"-message, but it don't



 What's wrong??  I really need some help here!  It's not my server, nor my computer, since I tried doing the same with the telnet command in the terminal, and there it worked just fine.

    Please help me!


--AntonKiland

Are you perhaps not sending the CR/LF pair to end the user line (i.e. @"USER ****'@*****.com\r\n")? 

Alternatively, I believe the Pantomime framework (available at http://www.collaboration-world.com/pantomime ) implements POP3 retrieval (in addition to a lot of the other low-level gruntwork involved in email handling) if you don't feel like implementing it yourself. -- Bo

Oh, I just think I've forgotten all about the CRLF-combination.. Major thanks! =)  --AntonKiland

----

I cannot seem to find any documentation for Pantomime anywhere.. Anyone who can help me out here?  Huge thanks!  --AntonKiland

----

Seems to be one of those 'the source code is the documentation' situations.  While I haven't tested Pantomime at all, the sequence of commands for POP3 retrieval seems to be:

*Create a POP3Store using the -initWithName:port: to specify the server and -authenticateWithUsername:password: to specify the username and password.  Both these methods are part of the Service protocol that POP3Store implements
*Call -defaultFolder (declared in the Store protocol) on the POP3Store to retrieve the folder.
*Call -allMessages on the POP3Folder object returned in the last step to get the messages.  Look at the Folder.h header to see all the other various methods supported by POP3Folder.
*Look in the Message.h and Part.h to see the various operations you can perform on the messages.

You'll probably also have to spawn a new thread to do this in since these POP3 commands all seem to be synchronous.  Truth be told, using this framework, for POP3 at least, seems like more work than a simple wrapper around NetSocket. -- Bo

