---
layout: page
title: CocoaMostWantedDeniedImpossibleWishes
---

----**Denied/Impossible Wishes:**----



* *Making good ol' MrC open-source* (Votes: AYES: 3 NAYS: 0)

Didn't Apple port MrC's optimizer to gcc?

No. gcc has no MrC code in it. MrC was entirely different in internal structure from gcc, which makes porting between the two impossible. Apple has been working on improving gcc's optimization and compile time, however.

Impossible; MrC is encumbered by contracts with Symantec, who provided the original front end.

----
Still unlikely, but the above is incorrect. MrC is made of up two parts: a derivative of the Zortech/Symantec front-end and a derivative of the Lucid back-end. The Lucid back-end was purchased by Tartan when Lucid's assets went up for auction. Tartan was later bought by Texas Instruments. It's believed that TI is no longer using any derived work from Lucid. Some former Lucid engineers now work on gcc and LLVM at Apple, however, none of them worked on MrC itself, which had plenty of Apple technology added to it. The MrC engineers have scattered around the industry or retired but amazingly they still occasionally meet for pizza.
----

*  *MachO should be revised and possibly replaced by a more modern ABI* (Votes: AYES: 4 NAYS: 0) [note6]

What about adopting CFM/PEF?

Impossible; Apple's former SVP of software invented the Mach-O format and has a large ego investment in it. Mach-O is also a lot cruftier, which makes it cool in the Unix world. Anyway, Mach-O is serviceable and deployed, so this is not gonna happen. It's water under the bridge. Let's move on.

----
CFM/PEF -- Nah, not with all the time we already spent un-adopting it. I'd like ELF, for standardization's sake, but I don't think Apple would want to put us through yet another phased transition, especially something without a lot of clear benefits like this. Avie left Apple, though, so I don't see why his "ego investment" would still matter.
----



----
----

*Back to CocoaMostWanted*

