---
layout: page
title: MultipleEditSyndrome
---

Stupid (but understandable) problem afflicting Wikis all over. Example:
    
contents of page S<nowiki/>omePageOnCocoaDev

Person A begins editing, loads "    contents of page S<nowiki/>omePageOnCocoaDev" into their computer's RAM...

Person B begins editing, loads "    contents of page S<nowiki/>omePageOnCocoaDev" into their computer's RAM...

Person A saves changes, sends "    contents of page S<nowiki/>omePageOnCocoaDev -- I'm called A!" back to the server...

Person B saves changes, sends "    contents of page S<nowiki/>omePageOnCocoaDev -- Go to www.apple.com!" back to the server...

Server obligingly overwrites the old copy of **S<nowiki/>omePageOnCocoaDev**, deleting A's edit.
Final result:
    
contents of page S<nowiki/>omePageOnCocoaDev -- Go to www.apple.com!


MultipleEditSyndrome is not that hard to fix, just click the "History" button, retrieve your changes from the overwritten version, and edit the page again. Note that this is only temporary cure, as MultipleEditSyndrome will inevitably infect the site again, probably on some big discussion.

----

Thanks to KritTer, who patched the Wiki software a while ago, multiple edit syndrome is now basically gone. If you start editing a page, but someone else saves their changes first, you will get a warning and two text boxes, where the top one has the new version of the page and the bottom has your version. When you click "Save Changes," the top box will be saved and the bottom box will be discarded. You can test this on the SandBox page by opening two edit windows at once, changing something, and trying to save them both.

----

*A program like FileMerge built into the Wiki software could fix this, but maybe not in the way that was originally intended.*

The problem with automagically merging revisions on a wiki is what to do with conflicts. You have three choices: pick one arbitrarily; display conflict markers and wait for the next editor to clean up the mess; or fail the merge and make the editor sort it out right away. The first two are unacceptable, because two editors trying to clean up after a failed merge can repeatedly conflict. The latter puts us right back where we started, but with extra complexity all 'round.

Herlihy proved in the early '90s that compare-and-swap trivially lets one achieve wait-free consensus among an infinite number of editors, while simple winner-takes-all can't even achieve consensus among two people. It seems best to adopt CAS for wikis, therefore ;)

