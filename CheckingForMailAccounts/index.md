---
layout: page
title: CheckingForMailAccounts
---

Is there any way that I can check and see if a user has email accounts set up in Mail?

----

This is a fragile way to go about doing it, but I imagine you could open com.apple.mail.plist and check in there. For syntax, just open up that file in the PropertyListEditor -- the accounts are listed by number, e.g., 0, 1, 2 and so on. I wouldn't do that, though. Probabably a more robust approach would be to see if there's an applescript way. 

--ShamylZakariya

----

Yea, all I need to do is confirm that an account is set up.

----

Here's the Applescript to get the name of all the accounts:

    
tell application "Mail"
	get name of every account
end tell



if you just wanted to check that at least one account is set-up you could do this: - it returns the number of accounts.

    
tell application "Mail"
	get count of accounts
end tell


or even:

    
	tell app "Mail" to get primary email



Of course these assume that your user is using Apple's Mail App.
--DiggoryLaycock

----
You could also add Message.framework to your project and use     + (BOOL)hasDeliveryClassBeenConfigured;
    // Returns YES if delivery accounts have been configured or not.

----

Is there a bug in the code or a bug in the comment? "Returns YES if delivery accounts have been configured or not." would indicate (at least to me) that the method returns YES regardless of whether accounts have been configured.

----

Well, I'm going to go out on a limb and assume that that's nothing but a bit of normal english language vagary. I think the *meaning* is that the message returns YES iff accounts have been configured. Otherwise, NO. --ShamylZakariya

----

Depending on what needs to be done, Message.framework could be used to send the mail as well. EDFrameworks also have a mail delivery class.

