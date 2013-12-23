---
layout: page
title: UseAppModalPanelInsteadOfSheet
---

My application is a "viewer" of sorts for a custom bundle type I've designed. Now, since the app is registered to be the primary viewer for the bundle type, you can double-click the bundles, drop them on the dock icon, etc, to open them. Of course, you can also open them directly via a standard General/NSOpenPanel running as a sheet.

When my loading mechanism detects an error ( for example: bad file data formatting; my app is a viewer, and as such I often edit the data files in General/SubEthaEdit and reload) a sheet opens describing the error ( well, it *will*, right now it just points you to my runtime log). So, should you drop an invalid file on the dock, or double click it, or call "open" from the command line, the error sheet will open and everything's good.

The trouble is when I open an invalid file using the General/NSOpenPanel running as a sheet. What happens is I perform the opening of the file in my openPanelDidEnd: callback, which is called as soon as the user selects the file and hits return -- this means the error occurs *before* the sheet has begun to retract. What happens is the app window *disappears* and the error sheet floats, disembodied on the screen.

If I click either of the buttons, the sheet slides away into nothingness and my GUI is gone.

My guess would be to wait for the open panel sheet to close before showing the error sheet. But since different systems are different speeds, I can't just assume it will take 1 second or something. And the openPanelDidEnd is called before the sheet closing animation begins, so that's no help.

Can anybody help me?

--General/ShamylZakariya

----

Can you show the error sheet as a modal panel instead? I believe the HIG discourages showing multiple sheets in series like this.

----

Open panels are typically modal dialogues rather than sheets, since the document presumably isn't open yet. Maybe that would help with your problem.

*The open sheet is probably attached to some sort of project management-type window.*

Yes. My program is semi-document centric with a persistent project management interface; when no project is loaded it shows the interface but an empty simulation view. It sounds odd, but it really is the way to go since my program is by design single-document-at-a-time.

... I just re-implemented it as app-modal and it works fine.
