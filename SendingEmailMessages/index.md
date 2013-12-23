---
layout: page
title: SendingEmailMessages
---

See also SendingEmail

----

Using the AppKit class NSWorkspace you can have your Cocoa application create a new message in Mail using the following snippet of code:

    

NSString *emailAddress = @"someone@somewhere.com";
NSString *mailToString = @"mailto:";
NSString *emailURLString = [mailToString stringByAppendingString:emailAddress];
NSURL *emailURL = [NSURL URLWithString:emailURLString];
[[NSWorkspace sharedWorkspace] openURL:emailURL];



or the compactified version:

    

[[NSWorkspace sharedWorkspace] openURL:[NSURL URLWithString:@"mailto:someone@somewhere.com"]];



I don't know much about the URL standard, but a prefix of mailto: indicates that the URL should be handled by the default Mail application (although it always opens Mail.app for me, regardless of what my default email app is).  



The next question is how can you attach a file using the mailto: syntax? I can send email using NSMailDelivery and even send attachments. But, what I'm really looking to do is to be able to popup the default mail app (whatever that may be), attach a file and let the user enter the email address and subject. 

-jth

----
Hey,

How would I go about using attachments with this.  I need tyo be able to support attachments with what I'm doing.  The attachments won't be large (really just log files), and will be sent with a bug reporting app.  Thanks for the help.

Chris Giddings
President, Ripple Software


chris.giddings AT ripplesw.net

----

If you would like to send additional data along with the mail, I don't know how to attach files, but you can use formatted strings to put data into the body of the mail. Here is my code to submit a bug report with a dump of two objects that are relevant for debugging my app.

    

-(IBAction)submitEmailBugReport:(id)sender
{

    // This line defines our entire mailto link. Notice that the link is formed
    // like a standard GET query might be formed, with each parameter, subject
    // and body, follow the email address with a ? and are separated by a &.
    // I use the %@ formatting string to add the contents of the lastResult and
    // songData objects to the body of the message. You should change these to
    // whatever information you want to include in the body.
    NSString* mailtoLink = [NSString
        stringWithFormat:@"mailto:sam@flexistentialist.org?subject=iScrobbler
        Bug Report&body=--Please explain the circumstances of the bug
        here--\nThanks for contributing!\n\nResult Data
        Dump:\n\n%@\n\n%@",lastResult,songData];
    
    // This creates a URL string by adding percent escapes. Since the URL is
    // just being used locally, I don't know if this is always necessary,
    // however I thought it would be a good idea to stick to standards.
    NSURL *url = [NSURL URLWithString:[(NSString*)
        CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)mailtoLink,
        NULL, NULL, kCFStringEncodingUTF8) autorelease]];

    // This just opens the URL in the workspace, to be opened up by Mail.app,
    // with data already entered in the subject, to and body fields.
    [[NSWorkspace sharedWorkspace] openURL:url];

}



----

On machines that have postfix configured and enabled, you can send emails like this:

    
BOOL sendEMail( NSString * message, NSString *subject, NSArray * recepients )
{
	NSCParameterAssert( message && subject && recepients && [recepients count] );
	NSTask *sendMailTask;
	NSPipe *messagePipe;
	NSString *messageHeader;
	NSData *messageData;
	int status;
	
	messageHeader = [NSString stringWithFormat:@"To: %@\nSubject: %@\n",
		[recepients componentsJoinedByString:@", "], subject];
	messageData = [[NSString stringWithFormat:@"%@\n%@\n.\n",
		messageHeader, message] dataUsingEncoding:NSNonLossyASCIIStringEncoding
							 allowLossyConversion:YES];
	
	sendMailTask = [[NSTask alloc] init];
	messagePipe = [[NSPipe alloc] init];
	[sendMailTask setLaunchPath:@"/usr/sbin/sendmail"];
	[sendMailTask setArguments:recepients];
	[sendMailTask setStandardOutput:[NSFileHandle fileHandleWithNullDevice]];
	[sendMailTask setStandardError:[NSFileHandle fileHandleWithStandardError]];
	[sendMailTask setStandardInput:messagePipe];
	
	[sendMailTask launch];
	messagePipe fileHandleForWriting] writeData:messageData];
	[sendMailTask waitUntilExit];
	
	status = [sendMailTask terminationStatus];
	
	[messagePipe release];
	[sendMailTask release];
	
	return (0==status);
}


Note that this will **not** work on an off-the-shelf installation of Mac OS X.

-Daniel Eggert

----

According to Apple Q&As QA1084 ( http://developer.apple.com/qa/qa2004/qa1084.html ) and QA1018 ( http://developer.apple.com/qa/qa2001/qa1018.html ), you cannot send an attachment to Mail with a mailto URL. Mail does not support the attachment part of the mailto protocol... too bad.
Since Mail is the default email client, that essentially ruled out the mailto URL for us.

We use [[AppleScript, as suggested by Apple in the same Q&A, and it works flawlessly (actually Apple own examples are incorrect and you need to adapt them slightly but the general principles outlined in the Q&A work).

-- BenoitMarchal

----

There are frameworks for doing this. I can vouch for EDMessage, part of the EDFrameworks. Check out ObjectLibrary for a couple more.

----

CSMail might come in useful if you're reading this page. Unlike most of the techniques mentioned here, it supports attachments and is not mail-client specific (this is achieved via plug-in modules... currently there is support for Mail.app, Entourage and Eudora; we're very keen on adding support for other clients, so if any mail client developers would like to support it, it's quite easy as the object model is currently very simple). It's distributed under a BSD-style license.

----

I'm losing my hair. I need to popup the mail app with new message, paste **rich text** in the body of the message and let the user enter the email address and subject. It's certainly possible (for e.g. this app is doing exactly the same thing: http://www.vojousoftware.com/inotepad.html)
With AppleScript it's only possible to create a new message from plain text string or am I missing something?

----

I've written an e-mail framework that makes it easy to work with IMAP and SMTP. It's very easy to send a message with it, but it requires it's own settings, it doesn't use an existing e-mail app like Mail.app. In particular the framework is designed for working with IMAP/SMTP and it's the base of a new email app i'm working on known as Kiwi. Anyway, you can check out MailCore [http://www.theronge.com/mailcore/]

----

Here's code using Message.framework for emailing an attachment and a plain ol' string body. Can easily be modified for normal NSAttributedStrings for formatting and multiple files --KevinWojniak

    
+ (BOOL)sendEmailTo:(NSString *)to subject:(NSString *)subject body:(NSString *)messageBody attachment:(NSString *)filePath
{
	NSDictionary *headers;
	NSMutableAttributedString *msg;
	
	msg = [[[NSMutableAttributedString alloc] initWithString:(messageBody ? messageBody : @"")] autorelease];
	
	if (filePath && [[NSFileManager defaultManager] fileExistsAtPath:filePath])
	{
		NSTextAttachment *ta = [[[NSTextAttachment alloc] initWithFileWrapper:[[[NSFileWrapper alloc] initWithPath:filePath] autorelease]] autorelease];
		if (ta)
			[msg appendAttributedString:[NSAttributedString attributedStringWithAttachment:ta]];
	}
	
	headers = [NSDictionary dictionaryWithObjectsAndKeys:
		@"noreply@mycompany.com", @"From",
		to, @"To",
		subject, @"Subject",
		@"Apple Message", @"X-Mailer",
		@"multipart/mixed", @"Content-Type",
		@"1.0", @"Mime-Version",
		nil];
	
	return [NSMailDelivery deliverMessage:msg
								  headers:headers
								   format:NSMIMEMailFormat
								 protocol: NSSMTPDeliveryProtocol];
}


I've tried this method in my code and it doesn't work properly. My SMTP server returns this error message:

 Action: Failed
 Status: 5.7.0 (other or undefined security status)

I don't understand why ? (Mail.app works properly with the same mail).
Join me at clairdev@hotmail.fr.


----

As mentioned elsewhere, NSMailDelivery uses account settings from Apple Mail. I don't see why this requires the user actually have Mail as their default mail reader,though - they can still be using Eudora or whatever, as long as they set up an account in Mail. They may need to use Mail preferences anyway to set the default email reader (much like setting the default web browser in Safari prefs).

Almost Too Good To Be True Dept.: NSMailDelivery seems to select the mac.com SMTP server according when the FROM address you pass in the headers dictionary is a mac.com address. At least that's what I just saw in OS X 10.4.10. I tried to get it to use a secondary SMTP server, but non-mac.com addresses all seem to use the SMTP server of the primary account in Mail preferences.

It looks like I'll have to send an apple event to Mail to learn the user's preferred from address, though. For now, though, I think I'll check My Card in the address book instead.

So far, the main drawback to NSMailDelivery, for me, is the lack of any specific error return, just YES/NO.

