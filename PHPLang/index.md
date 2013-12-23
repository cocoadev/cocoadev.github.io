---
layout: page
title: PHPLang
---



PHP is a scripting language used mostly for creating dynamic web pages. PHP is open source and exists on many platforms, including Mac OS X/Darwin. It comes preinstalled on Mac OS X Server, but may require activation in the httpd configuration file. Also, binary packages can be downloaded from the following URL:

http://www.entropy.ch/software/macosx/php/

PHP has a C-like syntax, limited support for object orientation and is powerful because of the way one can intermix HTML and PHP.

http://www.php.net/

----

There's now a Cocoa-style development framework for PHP, called PHOCOA, at http://phocoa.com. After learning Cocoa I found it too painful to write PHP apps in the "traditional" way, so I decided to make Cocoa for PHP. Now PHP is fun again!  -Alan

----

What I'd really like to see is a hybrid of PHP, Modula2, and Cocoa. We could call it Modiphoca.

----
Well we can't have everything we want, now can we? ;-)

----

And thank goodness for that, I rejoin.  ?:)

----

A few first impressions:

*Looks like an interesting idea.
*Whoa! Those examples are U-G-L-Y ugly! The project needs someone with marketing and/or graphics expertise, stat!
*The user guide looks useful, but needs some polish. Why, for example, are there two title pages and a blank page before you even get to the table of contents?
*User Guide, Ch.2 says: "PHOCOA's controller layer is proprietary..." but the web page says: "PHOCOA is open-source..." I don't get that.
*How is this better than, say, WebObjects?
*A big part of the appeal of PHP is simplicity. It's easy to understand how it works, and it's easy to get started. Some of that seems to get lost with PHOCOA, at least judging by the user guide.


----

Thanks for checking it out! Yes, I agree, it needs some marketing and documentation help. But you gotta start somewhere! In fact today, I re-factored the examples stuff. I think we have a much better approach now, cookbook-style, with simple examples. Are you volunteering to help? :)

The user guide is done in DocBook, and the PDF was done via xsltproc + fo to pdf. I don't know how to tweak it yet; I am not sure what's supposed to be on those 2 blank pages, but I am sure someting is ;)

Bad choice of words on "proprietary". I have fixed that...

I am not sure it's better than WebObjects. I've never actually used WebObjects, just read a bunch of docs on it. I heavily considered using WO instead of writing PHOCOA. Especially since WO keeps getting cheaper and cheaper. But it's still not FOSS, and also I am concerned that it's not progressing any more (publicly at least). I know it powers iTMS and the Apple Store and .Mac, but it hasn't had any real new features in years. In the end, I decided to just stay on the FOSS side and write PHOCOA.

I think a big part of PHP's appeal is simplicity; and a lot of low-end coders use it for that reason, on low-end projects. However, as developers and projects and infrastructure mature, there's a growing need at the high end for more robust solutions and frameworks, and you're seeing those now. PHOCOA is by no means the first or last PHP framework. There are loads. But I love Cocoa, and as hard as I looked I couldn't find any Cocoa-inspired PHP framework. There's a .NET one (Prado) and many Struts/MVC ones. So now PHOCOA is representin' for the Cocoa-lovers out there.

-Alan

----

Anyone used Phocoa? Do you know how easy is it to install?
In other words, if I have no shell access, root access etc (only FTP and MySQL access), can I use it?

----

It's easy to install, but you do have to edit the apache conf file. If you can make a small change to the apache conf file, then you can use PHOCOA. You could just install it locally on your machine then FTP the code up to the server.

