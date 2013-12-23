---
layout: page
title: WhoPostedNotification
---

Is there a way to determine who posted a distributed notification?  I'm seeing a lot of O3EngineChanged Notifications on my system and I'm trying to track
down the source of these.  Looking At the General/NSNotification and General/NSDistributedNotification Docs have not helped.  Any Suggestions?

----
Doing     [notification object] should tell you who sent it. However, having the correct object relies on the sender cooperating; if it's sending something cryptic then you're probably just doomed.

AFAIK the O3 stuff comes from the Bezel Engine and is sent when i.e. the Ambient Brightness changes for a General/PowerBook.

That's what it looks like thanks - KO
