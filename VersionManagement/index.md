---
layout: page
title: VersionManagement
---

Hi all, 

I have a little question and I think this is the best place to ask. I have old machine running Mac OS 9 and new one with OS X. I want to setup version management system on OS 9 that I could access with OS X. Did anybody tried this? CVS doesn't seems to work with Mac OS 9 (there are clients, but no server?) 

Thanks, 

Aidas 

----

You'll be much better off doing it the other way around: hosting the CVS repository on X and accessing it from 9. But let's assume you have some good reason for not doing that. A google search for cvs servers on 9 turns up nothing. You could try porting CVS yourself. Really, you'll be much better off doing it the other way around.

Other versioning systems for Mac OS 9 used to exist, but a quick search didn't turn anything up. Maybe they're all gone now.

-- General/MikeTrent

Using General/PowerMachTen and porting CVS might work...
