---
layout: page
title: MailCore
---

MailCore is a Cocoa e-mail framework built by Matt Ronge that is the core of the e-mail client Kiwi. MailCore provides a nice set of objects for working with IMAP, MIME and SMTP, all of which use the C e-mail library LibEtPan.

Documentation is available: [http://www.theronge.com/MailCore/API/]
Download from: [http://www.theronge.com/MailCore/]
Site: [http://www.theronge.com/mailcore/]

----

From your site:
    
CTCoreAccount *account = [[CTCoreAccount alloc] init];
[account connectToServer:@"mail.theronge.com" port:143 connectionType:CONNECTION_TYPE_PLAIN authType:IMAP_AUTH_TYPE_PLAIN login:@"test" password:@"none"];


You may want to make this more Cocoa-ish and use enumerations instead of what appears to be #defines:

    
CTCoreAccount *account = [[CTCoreAccount alloc] init];
[account connectToServer:@"mail.theronge.com" port:143 connectionType:CTPlainConnection authType:CTIMAPAuthPlain login:@"test" password:@"none"];


Or something like that... :-)
----
I just downloaded the framework and tried its example. but it is not working for me. when i tried sending mail it always gives the same error saying
"An error occured while sending the from address". though my  from address is perfect!.

