---
layout: page
title: TxtRecordsInBonjour
---



The General/NSNetService class has all sorts of methods for extracting General/TXTRecords, but the creating step is mysteriously absent, unless you have an General/NSData object with the General/TXTRecord conveniently already formatted.  how do I take a simple General/NSDictionary and convert that to a General/TXTRecord?

General/EcumeDesJours

----
+ (General/NSData *)dataFromTXTRecordDictionary:(General/NSDictionary *)txtDictionary� sounds promising

----
yeah - that function above fails silently (perhaps because I don't know how to implement it?) and prevents the nsnetservice from publishing altogether (all without any errors!) 

here's the code I am using (which fails):

assuming an General/NSNetService * service:

	General/NSDictionary * txtRecordDataDictionary = General/[NSDictionary dictionaryWithObject:@"1.22" forKey:@"version"];
       [service setTXTRecordData:[service dataFromTXTRecordDictionary:txtRecordDataDictionary]];

----
It is important to note that you must resolve the General/NSNetService before you attempt to read the TXT Record. The dictionary conversion does in fact work, but if you try to read the TXT Record before resolving you will get back an General/NSData object of length 0. ADC has a Q&A on this http://developer.apple.com/qa/qa2004/qa1389.html

I publish before I set the TXT Record, but I've tried publishing after and that works as well. One thing I see as a problem is that you are calling dataFromTXTRecordDictionary on the instance of General/NSNetService instead of the class itself. I tried that just to see what would happen and the app crashed at that point. Note that setTXTRecordData returns a BOOL to indicate success or failure. Also the console.log will contain an error message if the format of the TXT Record is not acceptable.

-- General/StephanCleaves

----

Thanks Stephan,

calling that method on the class itself did the trick.  Here's the working code snippet:

assuming an General/NSNetService * service:

     


	General/NSDictionary * txtRecordDataDictionary = General/[NSDictionary dictionaryWithObject:@"1.22" forKey:@"version"];
       [service setTXTRecordData:General/[NSNetService dataFromTXTRecordDictionary:txtRecordDataDictionary]];

 

-General/EcumeDesJours
