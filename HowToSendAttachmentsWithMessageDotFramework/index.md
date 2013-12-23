---
layout: page
title: HowToSendAttachmentsWithMessageDotFramework
---

Hey,

Working with Message.framework I've noted some difficulties sending and working with attachments.  Has anyone else has this problem, or dealt with this?  I know that there was a post to this effect in the tutorials area, I made a post there a few days ago but there hasn't been much attention payed to it.

What I need to do is send some log files as attachments with my application which is built to provide feedback to my team from our end users.  The log files of course would provide us with information on the last few running moments of our software, should it crash.

Thanks for the help.

Chris Giddings

chris.giddings AT ripplesw.net
[updated email address]

----

No specific experience with Message.framework but in our app, we had a need to send attachments. We ended up scripting Mail (as per QA1018, see http://developer.apple.com/qa/qa2001/qa1018.html for the details). Note that the script posted in the Q&A does not work... you need to adapt it slightly but the principle outlined there works.

-- BenoitMarchal

