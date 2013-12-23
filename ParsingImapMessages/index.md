---
layout: page
title: ParsingImapMessages
---

How do I extract the attachments from an IMAP message? I've read, that there is a way to use the boundaries.
Do you have any code samples or hints?

    

* 1 FETCH (RFC822.TEXT {2123}
--Apple-Mail-8--101733454
Content-Type: text/plain;
	charset=US-ASCII;
	format=flowed
Content-Transfer-Encoding: 7bit

Hi Mike,

I've got that report done.

Regards,

Jack


--Apple-Mail-8--101733454
Content-Disposition: attachment;
	filename=january-sales.rpc
Content-Type: application/x-rpt;
	x-unix-mode=0644;
	name="january-sales.rpc"
Content-Transfer-Encoding: base64

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
AAAA3QUAAAAA

--Apple-Mail-8--101733454
Content-Type: text/plain;
	charset=US-ASCII;
	format=flowed
Content-Transfer-Encoding: 7bit



--Apple-Mail-8--101733454--
 FLAGS (\Seen \Recent XAOL-READ XAOL-GOODCHECK-DONE))




----

Read header section of this message. Then, extract the boundary value from Content-Type: header field. This boundary string is delimiter of each part of message body. In your case, the boundary string seems to be "--Apple-Mail-8--101733454--".

