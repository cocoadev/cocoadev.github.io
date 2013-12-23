---
layout: page
title: CVSEmailNotification
---

Does anybody know how to keep a list of users who are notified everytime a CVS commit is made?  Thanks in advance!

----

Indeed ;-)

In the checked-out sandbox do a 
<code>
cvs watchers [filenames]
</code>
which gives you a list of all the people who are currently watching the specified files (in this case all of the files).

The watchers would normally create their watch with:
<code>
cvs watch add -a commit [filenames]
</code>

But this still doesn't give you mail notification, because the CVS special file **notify** defaults to doing nothing.

So the next step would be to **checkout** the special module **CVSROOT** somewhere convienent (e.g. /tmp). In there you will find a file called notify.
In **notify** uncomment the last line (The file even has rudimentary documentation ;-)) and commit the file.
CVS will say that it has rebuild the administration database and from now on will happily send you email when someone committed a file.
Note, however, that CVS will not notify you, if you committed the file yourself, i.e. you will never receive notifications about your own deeds.

--KayRoepke


Aaah, one more thing: On MacOSX the mail notification will probably not work out of the box, because of the drunken sendmail configuration. If you don't get any mails from CVS go and look into /var/log/mail.log for more information. There you should at least see the recipients and the subject 'CVS Notification'.
As I'm no expert (at least not right now ;-)) on sendmail config I cannot offer any insight on this.
You might want to look in /etc/mail/README and Apple's documentation for a solution on this.

--KayRoepke

Now to your question ;-)

There is no such thing as 'one single file' which contains all the users who are to be notified.
Instead this information is scattered all over the repository in a file called **fileattr** in the CVS special directory. If you need a list, I would recommend to write a shell or perl script to gather the information or call
<code>
cvs watchers
</code>
on all sandboxes you have.

--KayRoepke

----

Oh Poop!  I get the following error: when doing a CVS commit and trying to notify by mail

WARNING: RunAsGid for MSP ignored, check group ids (egid=20, want=25)
can not chdir(/var/spool/clientmqueue/): Permission denied
Program mode requires special privileges, e.g., root or TrustedUser.
WARNING: RunAsGid for MSP ignored, check group ids (egid=20, want=25)
can not chdir(/var/spool/clientmqueue/): Permission denied
Program mode requires special privileges, e.g., root or TrustedUser.

Does anybody know how to fix this!?  Help!!!  PLEASE! Thanks!

