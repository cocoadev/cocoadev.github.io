---
layout: page
title: XCodeAndNibFilesWithPerforce
---

As most of us have discovered in the past, General/XCode's SCM integration does not work all that well when it comes to NIB files.  There are workarounds for CVS as described in General/XCodeAndNibFilesWithCVS, but there were none for Perforce.  This frustrated me enough that I wrote a little hacky wrapper around the 'p4' command line client which allows it to to treat .nib and .framework bundles as if they were files; behind the scenes it tarballs them up for in the repository.  Thus far, it seems to work pretty well, at least for me.

The one gotcha is that Interface Builder does not know how to use SCM.  If you attempt to edit a NIB file you don't have 'open for edit' in Perforce, Interface Builder will whine that the NIB is read-only.  (This is deliberate on my part in the wrapper.)  Just select the NIB in Xcode and select 'Edit' from the SCM options.  Voila, the NIB can be edited, and it now is in the 'need commit' list under Xcode's SCM state as well.

http://www.riverdark.net/p4wrap.txt is the actual script; just take /usr/local/bin/p4 and rename it to /usr/local/bin/p4.orig, then copy the p4wrap file over as /usr/local/bin/p4 (and make it executable).  The rest should happen transparently.  Do note that you probably want to not have the project in Perforce yet, as you'll want to get the NIB files committed under this.

Hopefully this is useful to others!
    --General/RachelBlackman

The link is broken!
