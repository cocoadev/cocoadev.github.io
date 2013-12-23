---
layout: page
title: PackagemakerQuestions
---

Hi,

I recently needed to create an installer for OS X.   I really havent done anything for a mac for awhile (5 years ago, some applescripting) but we have a director application I need an installer for.  I'm having a hard time finding good resources/discussion groups on what I'm trying to do (tried applescript forums... thinking in the future I can use the applescript studio -which looks like you can build interfaces for with another tool, not facespan) so I apoligize if I'm in the wrong spot here.  Any sense of direction would be appreciated. I was directed here from a post in apples disscusion groups.

Anyway, what I'm trying to do:
I have an director application that I need to build an installer for.  After what research I did, it looked like packagemaker would be perfect.  It was simple to build a meta package (I want an option for a partial/full install), but it seemed the only way I could get it to work, the user would have to click customize and select/deselect a feature.  I was also not able to find where I can change the text on the screen to at least explain what they need to do to get a partial/full installation.  Either Packagemaker is pretty limited, or I just can find the documentation I need (I did find the packagemaker site... change welcome text, license, etc...)  I would really like to just ask the user if they want a partial/full install, and take appropriate action.

So my other thought is the simple (what seems to be the mac way of doing things) drag this folder to your drive, but the partial/full concerns a large number of quicktime movies, and can't think of a way to organize it on the CD without doubling up on a lot of the files... and take into account I'm also sharing this cd with a PC side - which needs a certain stucture to it.... I am sharing the movie files (which are needed for the partial install) to save space, but the the pc need a certain structure to it.

Yet another thought, I saw the new 'applescript studio' which looks pretty neat (XCode-the visual studio for the mac, and free??), and maybe would not be that hard to build an application that would pretty closely resemble the OSX look --with the inteface builder, tied to applescript? I haven't written any real applescript for 5 years, but that's pretty simple stuff...Especially just copying some files(If I can tie it to interface buider)  So if there is there is another way I could do this with XCode, I'd be up for that too... I have experience on the PC and AS/400 more recently... but if basic C++ isn't much different, and it looks like there is a compiler in there (forgive me, I just haven't spent much time on a mac for a long while) and I don't need to purchase much......

So, if this makes any sense to anyone, please point me in the right direction.  I'll be having more and more project I'll be needing to build stuff for Mac on in the coming year....
Thank for any response,
Jason

jreynoldsXX@XXupwritepress.com  (remove the X's to reply to my email)

-----

Here's a good HOW-TO on creating packages for installers: http://s.sudre.free.fr/Stuff/PackageMaker_Howto.html

daniel

-----

Apple has a public mailing list for Installer questions: http://lists.apple.com/mailman/listinfo/installer-dev

--pmb

