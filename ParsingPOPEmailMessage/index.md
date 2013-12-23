---
layout: page
title: ParsingPOPEmailMessage
---



I've tried searching around for any classes or code that parses a POP email message, but haven't had much luck. Does anyone know of any? I'm basically only need to get the text of an email, and leave all the other stuff out (i.e. attachments, strip HTML formatting, etc). Oh, and I could use Perl/C/C++ code too, not just Objective-C. Thanks.

----
c-client (part of the IMAP server from UW) does a great job for what you need.
It's plain C.

Quote:
The UW IMAP tookit includes the following:

c-client library: an API (application programming interface) used to build email clients and servers, including support for IMAP,POP3, SMTP and NNTP protocols and for local mailbox file access on Unix and Windows
[..]

See http://www.washington.edu/imap/.

----

See also Pantomime, the framework half of the GNUMail email client.  

http://www.collaboration-world.com/cgi-bin/project/index.cgi?pid=3

