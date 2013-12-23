---
layout: page
title: WikiLikeThisOne
---



I'm absolutely loving this format. And I want to start one (or seven) of my own.  
The main Wiki site (Ward's) is a little overwhelming when trying to find a good candidate. I tried Kwiki and it started out fairly straight forward, but edits just broke. Not sure what I was doing wrong.

How much of General/CocoaDev is custom? Any suggestions for getting started towards something like this?

    ~General/BlaKe

----

I tried phpWiki (phpwiki.sourceforge.net) at this site: http://ittpoi.com/studiecirkel/phpwiki/, but it doesn't compare to the speed of this wiki. It is slow and difficult to configure. It has some features that are useful, but others that just add complexity. As I understand it, index.pl is an atomic script that Steven modified significantly. Would it be possible to get a copy?

-- General/DavidRemahl

----

I just got General/PWiki working quite nicely on my home machine. Sounds alot like phpwiki, some nice additional features, but they do add alot of complexity.  I like the image upload feature though, as it's somewhat of a requirement for what i want to do.

   ~General/BlaKe

----

The perl script currently running General/CocoaDev is a modified (mostly cosmetically) version of Ward's Original, which I downloaded from http://c2.com/cgi/hp?General/WikiInHyperPerl

I'd be happy to send a copy of my modified script to anyone who wants it.  Just mail me.  Address is on my home page.

-- General/StevenFrank

----

If you're a beginner to this kind of thing, note that OS X makes things easy. Here are some instructions on setting up a home-served copy of this site. After this, all you need to do is learn Perl. Easy!

Once you have the Perl code (from Steven, say), put it in ~www/CGI-Executables/ (AKA /Library/General/WebServer/CGI-Executables/). You'll probably want to be in the terminal for this, as an administrator. Ensure the owner and group of the code are both www (chown www:www index.pl).

Now you need to /etc/httpd/, where you should find httpd.conf. Find the line General/ServerName www.example.com (probably the second such line, as the first is a comment) and change it to your server name. In my case, that is General/ServerName cjp39.trin.cam.ac.uk. Ensure the line is not commented out (preceeded by a hash, #). Ignore this step if you don't have an IP address.

Now go down a few lines and change General/DocumentRoot to, say "/Library/General/WebServer". If the default, /Library/Webserver/Documents, is fine for you then leave it.

You also want to check the General/ScriptAlias line is

General/ScriptAlias /cgi-bin/ "/Library/General/WebServer/CGI-Executables/"

Now you need to start your server up from System Preferences. Make sure you use your root password to allow changes, as this will make the server starts up as www, only able to access www-owned or www-grouped files, not as an administrator.

Finally, if you have a firewall, set it up to allow incoming Webserver services. If you still have problems, you may need to edit the config file by hand to allow UDP requests on port 80.

If someone could tell me how to link cgi-bin/index.pl to index.pl, I would be grateful. For now, you will have to make a link in /Library/General/WebServer/index.html (or /Library/General/WebServer/Documents/index.html, or wherever), ensuring it is owned by group www.

Have fun!

-- General/KritTer

----

You could use a mod_rewrite rule to rewrite all requests to /index.pl to /cgi-bin/index.pl.
