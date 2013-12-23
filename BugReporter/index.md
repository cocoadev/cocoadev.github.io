---
layout: page
title: BugReporter
---

Radar, Apple's on-line bug reporting database. See http://developer.apple.com/bugreporter

In order to access RadarWeb, you must be an AppleDeveloperConnection member. You can register for free (Online) membership at http://connect.apple.com (As long as you're old enough!!).

If your bug is against an opensource component of Mac OS X, consider using the OpenDarwin bug reporter at http://www.opendarwin.org/

----

Is it possible to view existing bugs from other users? -- AdamAtlas

I don't think so; that's one of the items on CocoaMostWanted. -- Dustin Voss

----

No, it isn't, and I can see why Apple would want to keep it that way. They even revoked the www.opensource.apple.com/bugs interface to Radar for bugs marked opensource, so now there is absolutely no public interface to Radar except http://bugreport.apple.com.

-- FinlayDobbie

----

"... and I can see why Apple would want to keep it that way ..."

You say you can see why, but you don't actually say the reason. Care to elaborate on that?

-- AdamAtlas

Customers with proprietary information.  If people with IP concerns know their postings will be made public, they might not include sufficient information to reproduce the problem.  Plus folks could be more hesitant to post problems, lest it be a RTFM and they are held up for ridicule.

----

Is there any history to the name RadarWeb? Or just a product name?

-- PhilipQuinn

*Apple's internal interface to the bug database is an application called just 'Radar', hence the web interface is 'RadarWeb'. Once a bug is submitted, it's 'on Apple's radar'. I think that's about the whole story ;)*

I believe they have a contact tool that they use for dealing with e-mails from external developers called 'Sonar', too. 

----

It's not just customers with proprietary IP - the details of unreleased software products are guarded very closely by Apple because it has a particularly weird and fanatical user base. So the details of the internal discussion of bugs, their resolution status etc are all covered by NDA. Also, the details of things even in the report itself, could be related to security, etc, and therefore best kept under wraps too. Actually, note that even Apple engineers can't necessarily see all Radars - only those that they're deemed to need to know about :)

This is not to say that this is an optimal solution. I'd much prefer it if Apple's bug database was more open. I'm just saying that I can understand their position and the reasoning behind it. -- FinlayDobbie

----

29-March-07: Just noticed that Apple's bug reporter has been updated. It's all, like, cooler...

