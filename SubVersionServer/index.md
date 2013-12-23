---
layout: page
title: SubVersionServer
---

I did found several pages about General/SubVersion acting as a client. None about a server setup. So I hope nobody will complain that I created a new page for this.

For now I use a localy reposistory, but would like to use a server setup. I did find other pages about svnserver but used with apache cq webdav, but I do not want to use that. Also I have no experience with Fink, if that is what I also needs to use. Is it posible that someone would love to write down the steps or a tutorial to set this up? Thanks

*Not to be discouraging, but you'll probably have a much better chance of getting help with this if you ask in a more appropriate place, like a site dedicated to General/SubVersion.*
----
I did look but couldn't find how to compile and install it on OSX ( tiger, server ). Any specific pointers? I was thinking that when the steps are here available it would benefit everybody.

----

I have had a Mac Mini as a Subversion server for quite some time now. I've installed Subversion with Fink (the package is called "svn"), and I think that's the best and easiest way to go. I have learned everything I know about Subversion from the documentation: http://svnbook.red-bean.com.

First of all, there are three ways to access the repositories, once svn is installed: Apache 2, svnserve and ssh. I have tried all three, but I use ssh almost exclusively, and svnserve for a few things. Here are the differences:


*svnserve, the setup requires editing the inetd configuration, but the setup isn't very hard. The communication is not encrypted, but passwords are not send in clear text, so if the data isn't sensitive, that's no problem. Using svnserve means that you have to setup users on a repository basis, which is a pro and a con, at the same time.
*Apache 2, the repositories are available as General/WebDAV directories, which can be handy, but otherwise I'd say that the setup is more complex than it has advantages. Repositories can be accessed with HTTPS for encrypted communications. The user setup is similar to svnserve. 
*ssh, my preferred way to access the repositories. Requires all users to have an account on the server machine, which can be a problem if you have many users, and don't want all of them to have shell access, but ssh is secure and trustworthy. Accessing a repository with ssh is the same thing as accessing it locally, the repository will be written and read as the actual user, not a server user as with Apache 2 and svnserve. Because of this, users' and groups' access rights are really important.


It's possible to mix the three server setups, even within a single repository, it's not recommended, though. I've tried having a repository with two users accessing it through svnserve (what I call virtual users, since they are not users on the actual system) and one (me) using ssh, and that went really bad. Technically it's possible to do, it's described here: http://svnbook.red-bean.com/nightly/en/svn.serverconfig.multimethod.html, however, it doesn't quite work, even if you follow the steps precisely. The Berkeley DB databases (redundant acronym, I know) crashes constantly. I have read that if you use FSFS instead it works better, but I haven't tried it yet. So, my single most important advice is: use only one access method per repository.

If you use ssh to access the repositories, you need to make sure that all users are members of the same group, and that the files are owned by that group. I have a user called "svnuser", and a group called "svnuser" (that is "user" as in "uses svn"). All users that access repositories are in the svnuser group, and the repositories are owned by the user "svnuser" (this is actually to simplify svnserve access, since svnserve runs as the user svnuser). See http://svnbook.red-bean.com/nightly/en/svn.serverconfig.svnserve.html#svn.serverconfig.svnserve.sshtricks.

The most common problem I've had is with Berkeley DB, and I'm now going to migrate over to FSFS, the new dababase/filesystem format. See http://svnbook.red-bean.com/nightly/en/svn.reposadmin.html#svn.reposadmin.basics.backends.

If you have any more questions, don't hesitate to ask, or to contact me directly (iChat: iconara).

--General/TheoHultberg/Iconara
