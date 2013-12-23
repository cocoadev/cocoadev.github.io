---
layout: page
title: NSTaskPasswordsAndSetStandardInput
---

I have been trying to get setStandardInput to work properly.  I tested with just cat and it seemed to work pretty easily.  However, when you want to make a gui for something like scp, ssh, rsync, etc, it becomes more difficult.  All I want is to enter the password.  I have code like shown here: http://cocoadevcentral.com/articles/000031.php, except for rsync instead, and I tried adding an if statement to copyData: that tests for @"Password:" from stdout, and when that happens, uses stdin to print the password that I recieve from the user in a password field.  But, this doesn't seem to work(although i verified that it IS finding @"Password").  I think what is happening is that it is getting sent through a file descriptor that isn't stdin, maybe, and so I print the password, and it doesn't work because I am not supposed to be going through stdin, but through another file descriptor.  Any suggestions? -Micah
----
Are you remembering to ask the pipe for the     fileHandleForWriting: instead of     fileHandleForReading:? --JediKnil
----
Yeah, as I said, I tested with cat, just opened it up and from there it echos anything you put forward.  And that worked.  I think I am not putting it in the correct standard in.  You can check out what I have so far: http://www.demonoverlord.com/Backup.zip
I didn't set up rsync to read from the NSTableView yet, or put a password field in.  That was coming next after I got this working.  -Micah
----
i believe ssh reads its passwords and passphrases from STDERR.  if a tty is allocated, it reads from the tty directly, instead.  this allows commands like "ssh host perl < file.perl" to work with password authentication. -ZJR

