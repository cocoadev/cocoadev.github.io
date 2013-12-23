---
layout: page
title: ScriptingBridgeAndMail
---



I've been playing around with the new Scripting Bridge in Leopard and trying to send an email through Apples Mail, but for some reason I can't specify recipients in e-mails.

    
MailApplication *mail = [SBApplication applicationWithBundleIdentifier:@"com.apple.mail"];
MailOutgoingMessage *mess = [mail classForScriptingClass:@"outgoing message"] alloc] init];
[[MailToRecipient *to = [mail classForScriptingClass:@"to recipient"] alloc] init];
[[mail outgoingMessages] addObject:mess];
[[mess toRecipients] addObject:to];
mess.sender = @"my@address.com";  // The sender of the message
mess.subject = @"a subject";  // The subject of the message
mess.content = @"blablabla";
to.address = @"test@address.com";


Everything is fine until the last line. A new mail is created and filled with subject and content, but when to.address is set, it throws an exception.

    
2007-11-11 19:49:10.688 test[6665:10b] *** Terminating app due to uncaught exception '[[NSGenericException', reason: 'Apple event returned an error.  Event = 'core'\'setd'{ '----':'obj '{ 'want':'prop', 'from':'obj '{ 'from':'obj '{ 'from':'null'(), 'want':'bcke', 'form':'ID  ', 'seld':392386640 }, 'want':'trcp', 'form':'indx', 'seld':1 }, 'form':'prop', 'seld':'radd' }, 'data':'utxt'("test@address.com") }
Error info = {
    ErrorNumber = -1719;
    ErrorOffendingObject = <MailToRecipient @0x535c30: MailToRecipient 0 of MailOutgoingMessage id 392386640 of application "Mail" (4719)>;
}'


Error number -1719 is "Can't get <reference>. Invalid index". Any ideas why this happens?

// Mike


----

Answer: This is mostly Mail's fault for failing silently if you try to create a 'to recipient' without specifying an address as part of the 'make' command.

Example:

    
tell application "Mail"
	set m to make new outgoing message
	make new to recipient at end of to recipients of m
	count to recipients of m
end tell
--> 0


What Mail should do is create a recipient object with a placeholder value for the address, allowing the actual address to be filled in later. This is the recommended behaviour according to the Scripting Interface Guidelines. Alternatively, if there's some technical reason why this isn't an option, its 'make' command should raise an error if an address value isn't supplied via its 'with properties' parameter. You should file a bug on this.

Anyway, the correct command to use is:

    
make new to recipient at end of to recipients of theOutgoingMessage with properties {address:"test@address.com"}


For Scripting Bridge, see -[SBObject initWithProperties:].

Scripting Bridge also deserves blame here for obfuscating the heck out of various aspects of Apple event IPC, including object creation, making it difficult for the average user to understand what's actually going on beneath its pseudo-OO exterior. If Apple event IPC and Scripting Bridge really did operate in an object-oriented fashion a-la COM/CORBA, these three pieces of (Python) code would all do the same thing:

    
to = mail.classForScriptingClass_(u'to recipient').alloc().init()
mess.toRecipients().addObject_(to)
to.address = 'foo@bar.com'


    
to = mail.classForScriptingClass_(u'to recipient').alloc().init()
to.address = 'foo@bar.com'
mess.toRecipients().addObject_(to)


    
to = mail.classForScriptingClass_(u'to recipient').alloc().initWithProperties_({'address': 'foo@bar.com'})
mess.toRecipients().addObject_(to)


Instead, only one of these works as intended while the other two fail: one due to a bug in the target application, the other due to a leaky abstraction in Scripting Bridge. Of course, to understand which is which and why they succeed or fail, you'll need to know how Apple event IPC actually works (hint: RPC+queries) and then try to work out how Scripting Bridge performs it internally behind all the misleading foofaraw - although this rather defeats the point of pretending to provide a comfortingly familiar object-oriented interface in the first place.

-- hhas

p.s. For a less obfuscated and more reliable alternative to Scripting Bridge, see objc-appscript [http://appscript.sourceforge.net/objc-appscript.html] (self-link).

----


----

Without knowing much about Mail scripting, it looks like the address is a collection, and you're trying to set it to a string.  The clue is "MailToRecipient 0 of MailOutgoingMessage".  --K

----

This is what the interface looks like.
    
// An email recipient
@interface MailRecipient : MailItem
@property (copy) NSString *address;  // The recipients email address
@property (copy) NSString *name;  // The name used for display
@end

// An email recipient in the To: field
@interface MailToRecipient : MailRecipient
@end


// Mike

----

 Scripting Bridge falls down when it comes to collections. Running iTunes through sdp it generates the following method for iTunesPlaylist

    
- (iTunesTrack *) searchFor:(NSString *)for_ only:(iTunesESrA)only;  // search a playlist for tracks matching the search string. Identical to entering search text in the Search field in iTunes.


What it doesn't say is that unless the search yields only one track (even then, I haven't actually tested it) the method will return a collection, an array to be exact. So everytime I call this method I'm having to perform a runtime check for the class of the object to see if it has returned an array or a single track.

This may also be the issue you're having,

----
There's a similar issue with the Scripting Bridge and iTunes regarding cover artwork. Anyone know how to set cover artwork for a song in iTunes using the Scripting Bridge?

