---
layout: page
title: NSNotificationCenterBug
---

I just found this bug in NSNotificationCenter - couldn't find  it mentioned anywhere so thought I'd share.

If you do this:

    

[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(handler:) name:note_A object:nil];
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(anotherhandler:) name:note_B object:a_specific_object];



then later you try to remove yourself as an observer of any notifications from <a_specific_object> like this:

    

[[NSNotificationCenter defaultCenter] removeObserver:self name:nil object:a_specific_object];




You'll also find that notifications of all <note_A> types are removed. This doesn't seem like correct behaviour to me - in the first case I want to know about any notifications of <note_A> type from any object, in the second case I want to know only about <note_B> notifications, and possibly some others, from a_specific_object. When I'm done, I want to stop listening to any messages from a_specific_object, but continue to receive any others from other parts of the system. NSNotificationCenter, on receiving a nil parameter in <name> for removeObserver:name:object: apparently ignores the object: parameter and treats this as nil too. This is definitely not as documented, and not what you'd expect either. A workaround is to do a removeObserver for each notification type from a_specific_object  individually. Applies in 10.4.8, YMMV. --GrahamCox

Stupid question, but did you verify that a_specific_object is non-nil in the second case? A small test program would be helpful to have. Anyway, assuming it behaves as you say, the docs specifically state: "If notificationName is nil, anObserver is removed as an observer of all notifications containing anObject." So if that's true, sounds like bug report time.

----

Looking at my actual code, it's possible that a_specific_object can be nil in some corner cases. I do wonder now if that's what happened... I'll have to set up a situation to provoke that and double-check. So maybe I was a bit premature pointing the finger here - will let you know. --GC.

----
This is the correct behaviour. Specifying nil as the notification name means "all notification names", so removing self as an observer for name nil results in self being removed as an observer for all notifications.

----
No, it does not. RTFM. I even quoted it above. "All notifications" is nil notification name *and* nil object. Nil for only one of those parameters merely removes all notifications which match the other parameter.

