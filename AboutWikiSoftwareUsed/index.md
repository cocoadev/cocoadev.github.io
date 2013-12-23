---
layout: page
title: AboutWikiSoftwareUsed
---



**This site is a great WIKI implementation**  

Could someone tell me which WIKI software this site is using? Or on which WIKI software is it based?

----

The engine was founded on one of Ward's original Perl implementations, as documented on WikiWikiWeb. However, StevenFrank and, more recently, myself and others have hacked the original rather severely, adding among others:


* The unusual %% syntax
* The interface, with its recent fetching CSS facelift
* Full page history, Topics and footnotes
* No longer uses a database backend (it was terrible moving the site between platforms)


If you want the source, contact StevenFrank. It should run on any UN*X-based OS, including MacOSX.

-- KritTer

----

Indeed, my site works fantastic, and it's FAST. I do have a question though... I added 3 custom formatting commands: \\%\\%ATTEN\\%\\%, \\%\\%CLEAN\\%\\% and \\%\\%NPOV\\%\\%; Atten flags an article as needing work done to it, clean requests cleanup to an article and nopv flags an article as being in dispute (factual errors, opinion, et al.). I've been manually adding \\%\\%ATTEN\\%\\% to an article, along with \\%\\%BEGINENTRY\\%\\%PagesNeedingAttention\\%\\%ENDENTRY\\%\\%, so a user can quickly glance at PagesNeedingAttention and see entries for articles flagged with \\%\\%ATTEN\\%\\%. Is there a way a user could use the ATTEN flag and automatically have \\%\\%BEGINENTRY\\%\\%PagesNeedingAttention\\%\\%ENDENTRY\\%\\% appended to it? Yes, I know people can just check the RecentChanges, but I like having the html output of ATTEN, and using a page that dynamically updates when the special command is used.

Anyway, sorry to ramble. Any thoughts? Could this be done? -- ChristopherB

Edit: I created a 'hack' that allows users to add the code on the edit screen, using a simple form + buttons. I'm now trying to add a perl-based text counter and an output of the working directory to show how many articles have been created for the site, and have it outputted at the bottom of the pages http://bit.ly/dt3ruz.

----

I agree - this is by far the best wiki implementation around!
-- EcumeDesJours

----

Thanks!  For now, I've made the source available here:

http://www.cocoadev.com/source.tar.gz

As mentioned above, this is simply Ward's Original Wiki with a whole lot of extensions, and a pretty stylesheet.  The source is a bit of a mess.  The process of taking this source and starting a new site with it is not well-documented yet.  (Feel free to document here.)

At the very least, you'll need to:


* Change the $DefaultTitle variable to what you want your default home page to be named
* Change the hardcoded "CocoaDev" references embedded throughout the script
* Change the $wdir variable to something other than "CocoaDev" and make a directory with the same name at the same path as the perl script.  Make sure it's readable and writable by the web server.  This is where all your pages will be stored.


If you take the source and make changes (as we did for this site), you are encouraged to make your changes available, in the spirit of open-source software.

-- StevenFrank

As a point of interest, if you run the script off a MacOSX server on a case-insensitive volume (like most MacOSX ones are), you'll find that page names become case-insensitive. Sort of. -- KritTer

----
Love the wiki, curious if someone could point me in the right direction of using it, i have it up and working on my server but the "Home" link results in blank page
-- JeremyK

----
I have a modified version of the perl script that powers this Wiki up and running locally on my OS X client machine; I have been using it as a personal knowledge base. If I understand your problem correctly (and if I remember correctly as well); You do need to create the home-page itself, so if your perl script is still pointing at the page called CocoaDev, you need to create it. I would recommend changing the CocoaDev specifics in the script to something more relevant to your situation.

I will temporarily move my data directory to make sure I am not talking nonsense.
----
hmm, well i called my site JfroDevWiki, data folder in same directory is JfroDevWiki, and i created a new page called JfroDevWiki but still home page turns up blank
http://zeratul.ath.cx/cgi-bin/index.pl?RecentChanges
http://www.thefunnyquotessayings.com/cool-hilarious-funny-quotes-sayings/
clicking on home you'll see nothing, but you'll see a page listed there that is named same as the site
----
Ha, Did you adjust the permissions and owner/group on the data folder? I have mine set to 770 and www:staff.
----
yeah, i created a few pages in the wiki already and they work, just the home page doesn't load anything...
----
Ya, I just checked out your page, odd, I need to review the original perl script to refresh my memory...
What does your httpd error log say? If the script choked, thats where you will see it.
----
After looking at your page some more, I would bet your script is crashing. Check out your error log at     /var/log/httpd/error_log (assuming OS X).
----
nope, no errors, it just spits out a DOCTYPE and <html></html>
----
Oops, sorry; the first time I looked at the html source I didn't see the </html>. Back to looking at the original.
----
I was able to reproduce the behavior. Make sure     $DefaultTitle is set to the name of your main page without spaces. Or change the statement
    
if ($ENV{REQUEST_METHOD} eq GET)
{
    $RawInput = $ENV{QUERY_STRING} || $DefaultTitle;
    $RawInput =~ s/^($LinkPattern)/$DefaultRequest=$1/; 
} 

to use something other than     $DefaultTitle.

Cheers

----

Oh the simple mistakes!
fixed, thank you very much! i had a lowercase letter in there....

----

Ok... It just flashes, and reloads itself and does nothing beside that... -JoshaChapmanDodson

----

I think the reason it is doing this is because your Apache doesn't know that it should be executing the .pl files and it's just sending the .pl source to the browser. I am getting the same thing .. though am unsure as to how to fix it. -- MikeZornek

----

*Search for articles on Apache configuration on http://www.macdevcenter.com/ - that's how I learned to do most Apache configuration, including turning on PHP and telling it what files to be used for, which is pretty analogous to what you will probably have to do for Perl.*

----

MikeZornek : My install is kind of specialized but I eventually made it work by:


* added ExecCGI to the Options line for the directory declarion for the folder I am storing the website in.
* put index.pl at the top of my DirectoryIndex.
* uncommended the AddHandler for cgi and add .pl. End line looks like "AddHandler cgi-script .cgi .pl"


A few questions:

I was reading ShouldWeSwitchToTwiki and there is a lot of talk about this site developing a new PHP variant of the current CocoaDev source. What ever happened to this?  

*I got a prototype working, but decided the existing version was working well enough for now, thanks to Kritter's tweaks, and stopped development. -- StevenFrank*

Concerning the copyright notice at the top of the perl file, what kind of license is this under? Do I have to contact Ward to use this, make modifications, redistribute, such as you guys do? I'm a little confused.  

*As far as I know, it's free for the taking.  The source we started came from Ward's wiki here: http://c2.com/w4/wikibase/?WikiInStraightPerl but now ultimately leads you to a 404.  If you want to be absolutely sure, email Ward. -- StevenFrank*

As for myself, I'm making good inroads in applying the CocoaDev engine to a wiki of my own for here at work. The first big item I want to add is an RSS feed for the Recent Changes page, which upon a search lead me to the ShouldWeSwitchToTwiki discussion.
----
I was reading the MacDevCenter articles, and I could not find anything that I didn't have! Could I have a step-by-step tutorial on setting up this WikiWikiWeb. My Perl version is v5.8.1-RC3 if that helps. Thanks in advance. --JoshaChapmanDodson

----
Thanks for the cool wiki! I just set this up on Panther as follows:


*Added new user WikiWiki from System Preferences - Accounts
*Copied index.pl and style.css to /Users/wikiwiki/Sites
*Edited index.pl to change $wdir, $DefaultTitle, all instances of "CocoaDev", and the Quick Links. This equated to lines 59, 60, 69, 82, 342, 387, and 563.
*Created a directory to match $wdir. chowned this directory, index.pl, and style.css to wikiwiki:www. chmoded the directory to 775, index.pl to 755, and style.css to 554. 
*Edited /etc/httpd/users/wikiwiki.conf to read
    

# Hi, I'm new here but I had to add the following line to get this to work
Alias /Sites/ "/Users/wikiwiki/source-Folder/"
# End the newbies edit

<Directory "/Users/wikiwiki/Sites/">
    AddHandler cgi-script .pl
    Options Indexes MultiViews ExecCGI
    DirectoryIndex index.pl
    AllowOverride None
    Order allow,deny
    Allow from all
</Directory>

*I then copied FindPage, TextFormattingRules, HowToUseThisSite, and HowToCreateNewPages from this site. You'll want to edit these pages to be specific to your site.


Works great. Thanks again. --Dan

NewPage should be copied too I believe. Thanks for the directions. --OlivierScherler
----
Nice! I got my WikiWiki up! http://pixikon.com/server
Thanks StevenFrank, and Dan!

--JoshaChapmanDodson

WebDevWiki.com [http://goo.gl/JumDi] has launched which is based on this engine. Thanks to SteveFrank and KritTer for all there hard work on the engine! -- MikeZornek

----

Would it be too much to place an RFE that a feature be added to make RecentChanges uneditable?  I find this this whole deal with people editing RecentChanges to be completely counter to the spirit and purpose of the WikiWikiWeb.  I'd do this myself, but I'm not fluent enough in PerlLang.  --OwenAnderson

----

I felt that polling a wiki's RecentChanges page is a little inefficient for participants.  After downloading the software for this site I edited so that users can receive notifications for pages they are intereseted in.  

http://webdevwiki.com/index.pl?PageChangeNotifications

I submitted the change to MikeZornek who is hosting a wiki based on the software used by this site.

--MarkRosen

I kinda like polling the RecentChanges.  I come across pages I never would have known about otherwise.

----

One problem I've run into several times is if two users edit the same page simultaneously, one user will mistakenly override the other user's changes. As a solution to this problem, I suggest a "startEditTime" field be added to the edit form. If another edit has taken place after that time then it will notify the user submitting the change. If you want to go all out, you can report the differences of the two edits so the user doesn't have to look at the history. Does this sound like a good idea to anyone? -- RyanBates

----

Speaking of RecentChanges, I recently downloaded the source, and the recent changes is coming up backwards.  That is, April 14 shows up above April 15, so I have to scroll to the bottom to see what's new.  Is this a config setting, or do I have to hack the source to get it to behave like the RecentChanges here?

----

I've made some minor enhancements, heavily reformatted the source, and tried to create a simple easy to use installation process. Everything is packaged in a tar-gzip file which can be downloaded at [http://dargos.com/Downloads/Blark.tgz]. Be sure to actually read the README :)

--Cucumber

----

July 26, 2004 - I've posted a new version of the source that incorporates a few bug fixes that have accumulated over the last few months.  -- StevenFrank

----

August 4, 2004 - I've set up this source and it displays fine. However I don't seem to be able to edit or create any new pages. I don't think it's a permissions problem - I tried allowing read/write for everybody and it still didn't work.

*You may simply need to create the folder named in the $wdir variable - try that!*

----

Heh heh, thanks for the help. Actuallly I got this working a little while ago and am now making some modifications to the source. I'll put something up here once I have something to show.

*If you're interested in Wiki design, you might want to check out http://c2.com/cgi/wiki?WikiEngines on Ward's Wiki -- there's certainly a lot of inspiration there!*

----

December 2, 2004 - I installed the latest official release and I'm having trouble accessing the $DefaultTitle page. All the others work fine, but I get a completely white screen when going to the index, whether I specify the article name or not. The permissions are all right and the file format isn't different than any other. Scratching my head over this one.

*Oh, it didn't like that my $DefaultTitle wasn't in CamelCase. Hm.*

----
Yep, that'd do her. You can't create a page that's not got a CamelCase name, because then you couldn't link to it. If you want to support free links like Wikipedia does, you'll have to hack the code some. -- KritTer

----

I'm in the process of refining the wiki source for this site on my web site. I'm trying to make it so it doesn't use <tables> anymore, and is all CSS driven. I added a line of code that allows you to create external links the same way that MediaWiki does. Maybe KritTer can add it to this site?
    s/\[(http:\/\/.*?)\s(.*?)\]/<a href=\"$1\">$2<\/a>/g;
Example:
    [http://www.foxnews.com Fox News]
The only problem is you have to disable the code that generates links from full URLs, so it's either one of the other. -- KevinWojniak

----
I've rewritten the parser recently; it shouldn't be an either-or choice anymore. I've been planning on adding this syntax for a while, but keep procrastinating, sorry :/ I would insist on surrounding the external link with square brackets, though, so people have a visual indicator that the link's external. (This comes from Meatball.) -- KritTer

----
When was the source linked here last updated?  It would be nice if it was in an svn somewhere (thinking pie in the sky here).  -- Vinay

Seems that a lot of '%%' instructions are used... The effect is very cool.

----
Feature request
To remove spam - or am I the only one who notices - could we have a revert button?
Potential pitfalls include a revert function that offers to save changes, and/or a save changes function that restores the spammy version


Category:ObsoleteInformation

