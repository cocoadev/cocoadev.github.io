---
layout: page
title: HowITunesTracksSongFiles
---



How does iTunes track the music files? For example if I go to iTunes Music folder and change a name of a music file, iTunes is still able to play and show it in Finder.

----
I asume by using FSRef, FSSpec, Aliases and other yucky incompatible expectation dashing non standard but occasionally useful legacy Mac stuff.

"Bitter much?"
----
Incompatible with what and dashing what expectations?
----
There is no need to reopen the "paths are evil" vs. "paths are the only cross-platform network usable solution" debate.  http://groups.google.com/group/comp.sys.next.advocacy/browse_frm/thread/20a89e99350bd3ee/658711c8af03edaa?lnk=st&q=&rnum=2#658711c8af03edaa

I was being sarcastic because the original question highlights one strength of the traditional Mac approach, but I was anticipating critisism of the mere suggestion that old Mac stuff might be useful afterall.  I guess I have scabs from old debates.
Just to head off somebody else saying it: Yes, I know that there are sometimes issues with aliases using network file systems and unix commands and SMB shares and read only file systems etc.

----

Yep, I've got those scars too.

Personally I can't understand the people who think its a Carbon vs Cocoa situation.
The best thing about OSX is that we have a choice of APIs we can use: POSIX, Carbon and Cocoa. Bring it (all) on!


----

I don't understand why debate is even necessary. The AliasManager and FileManager APIs work very well and are available to any developer who wishes to use them. Value judgements do not affect their availability. Use the right API for the right purpose and get on with making your app actually useful.

