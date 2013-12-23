---
layout: page
title: MiniBundleAnnoyance
---

Ok, so I have a few little mini bundles in my program to provide some runtime components (for example, a few extra resources that I can change or have multiple versions of, like a theming system).  As of right now, I only have one "default".  Anyways, I have it added into my project so that it gets added into the internals of my program.  The problem is, changes in this (such as an xml file, or a plist) are not reflected when I recompile.  I have to manually clean, and build, which is a pain since the whole point is for it to be runtime and thus quick, not slower than making changes in code!  So anyways, my question is, how do I set some sort of dirty flag or something so that everything in my default folder (bundle) is replaced in the build application, so I dont have to clean.  Thank you.

- FranciscoTolmasky

If it's not part of your project's resources, add a copy files build phase to copy the file(s) where you want. This should always get the latest copy installed. But remember that changing any file will need a re-build (but not necessarily a clean) for the changes to take effect.

-Seb

I am not sure my answer will be useful. It is not clear what you do (how many targets, what do you 'recompile', in which bundle is the plist,...).
Anyway, here it goes:
If the mini bundle is part of your project (in a different target), you could also set a dependency, so that the mini bundle is rebuilt (updating the latest files in it), then your main app is rebuilt (and the updated bundle changed, thanks to the 'copy' phase mentioned by Seb). Without recompiling code.

