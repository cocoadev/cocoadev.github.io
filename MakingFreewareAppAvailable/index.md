---
layout: page
title: MakingFreewareAppAvailable
---



I have a couple small apps that I want to make available to others.    I'm wondering if there are any 'best practices' recommendations, or any tips for making it look a little more professional?

Can anyone point to any resources regarding topics relevant to this?  I'm thinking things like:

- Cleaning up an app for distribution.  Any gotchas to look out for?
- Packaging the app in a dmg, and making it look decent.

Then, there's the presentation.  With freeware, obviously I'm not wanting to spend much to do this.
- Hosting?  Do any sites host Mac freeware apps?
- Tips / resources for icons or www presentation?

----
You should search the site before creating a new page  .... see PublishingMyApp
----

Thanks for the response.  But, that page really doesn't answer any of the questions I posed.    It dealt mainly with shareware business practices, pricing, etc.    Obviously, those things don't come into play in free apps.   Also, inexpensive hosting, icons, etc. are a little tougher when you're not charging.

----
OK, here is some of the things you should need.
1) First, open all your nib files in Interface Builder. Go to Format>Validate Layout, and fix any validation errors you find.
2) Follow the Apple Human Interface Guidelines, available from Interface Builder's Help menu.
3) Go to your hard drive's /Developer/Applications/Utilities/ folder. The PackageMaker program will make an installable package, if your app depends on more than just the .app file.
4) To make a .dmg file: http://www.wikihow.com/Make-a-DMG-File-on-a-Mac
5) Any site with file uploading and downloading support can host a Mac app. Some things you should know: some freeware web hosting sites don't allow this. If you have a .Mac account, make sure you can get a website for it - because .Mac includes web hosting.
6) There are some good books that discuss web design. Find some at your local library.
7) Make one or more screenshots available.
8) Provide a form or email address so people can ask you questions or ask you for help.
9) If you have an image editor, you can make an icon. See HowDoIConvertImageToIcon (one of my questions :-) ).

Hope this helps! PietroGagliardi

----

Thanks for the reply, Pietro.. that's good info.


I'll add a few items that I've found so far, for the next total newbie looking for info.. 

- Create Credits.html document, with relevant information to be displayed in the "About Application" dialog.  Contact info, credits, licensing, etc.

- Update Info.plist, with relevant info, such as CFBundleIdentifier, and update InfoPlist.strings

- In XCode, under Project->Set Active Build Configuration, change to "Release".    I assume this does things like not include the debug symbols in the executable.

- Create DMG to hold app.   I used a sparseimage, so the image wouldn't be any larger than necessary.  

- Create background image for the DMG, with logo, instructions, etc.  Place in a hidden directory, and configure the folder View to use it as background (I'm still completing this,  I think there might be an extra hoop or two to jump through to get this to work correctly).   Here is a link with some great examples of DMG background layouts:   http://softwaretrenches.com/2006/09/dmg_disk_image_is_the.html

- Create ReadME.rtf file to be placed in DMG.   Probably containing info on requirements, installation, usage, and license.    For freeware, the license is probably something along the lines of 'This is free.  Use it if you like.  I'm not responsible if it kills your gerbil.'   GPL, Berkeley, X, MIT, or other common license would work, if amenable to you.

- pages.google.com seems to be a reasonable free hosting option.   It has limited storage space (100MB?), and probably a bandwidth cap, but is pretty unobtrusive otherwise.   Although the editing application is pretty limited, and it doesn't have an option for FTP/scp publishing.

- www.nexumoja.org is a free hosting service for software developers (mac only). They offer trac, SVN, FTP etc. and all the advertising centralized...

- RapidWeaver and SandVox are good options for WWW editing software, depending on your hosting service..  They are not easy to use with Google Pages, although I think you could manually copy the resulting documents.


I'm still struggling a bit with the graphic aspects of this.   An artist I am not, so I'm trying to find tools that will allow me to come up with a halfway professional looking www site banner, DMG background, etc.   

----

Here is a concise guide for making a customized DMG (building the DMG with your view options, and creating a compressed image..   doesn't include the background image stuff, but that's pretty easy):

http://jwz.livejournal.com/608927.html

And another, with background image detail:  http://www.decaffeinated.org/archives/2003/10/24/dmg
I find the sizing a bit tricky..  Finder doesn't always open the image in the sized window I'm expecting.  Also, sometimes it opens the image in a new window without the links on the left {Desktop, Applications, Documents, etc.}, and other times it opens in a normal finder window.

----
Macupdate.com hosts freeware apps for you.

