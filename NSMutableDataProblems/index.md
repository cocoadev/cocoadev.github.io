---
layout: page
title: NSMutableDataProblems
---

Hi guys, and thanks in advance for all assistance you may offer...
Im a NOOB and im having a slight problem ( read VAST ) with NSMutableData.... I have a file read into an NSMutableData Object, looking at the object in NSLog it reveals it as a stream of HEX DATA.. That is OK for me as i want to replace HEX DATA with HEX DATA. The problem is that I cant use replaceWithBytes with HEX DATA. Say i want to replace the HEX VALUE 6100 with 7100 it will not work ..6100 is 2bytes of data ( 61--00) while it seas 7100 as 4 bytes.. ( 7--1--0--0) so this overwrites not only the bytes i aim to overwrite but also the following next two bytes.... What am i doing wrong ? how can i do this ?.. i thought of converting the NSData to an NSString but this did not seem to work either.  
HELP !!
----
I'm not really sure what you're asking here - is your problem that you don't know how to change the contents of the block of memory managed by an instance of NSMutableData, or are you confused about the different ways that a block of memory can potentially be displayed (ie: that it is possible to treat a block of memory as a string, as hex values, as ints, as floats, as whatever, depending on your purposes)? Some of your source code might be nice, but here's what I'd do:
    
// Assume:
// newData exists, and is a scalar C type (eg. an int, not a pointer to a buffer)
//     in your case, it looks like you're trying to set 32-bit integer values, so an int or a long
//     is probably what you're after.
// newDataIndex exists, and is an unsigned int
// myMutableData exists, and is an instance of NSMutableData

[myMutableData replaceBytesInRange:NSMakeRange(newDataIndex,sizeof(newData)) withBytes:&newData]; 
 

----

There is no such thing as "hex data". Hex is a way to *represent* data. I think your problem is not knowing how to convert to and from a hexidecimal representation. The     strtol() function can convert from hex, and     sprintf() or     [NSString stringWithFormat:] can convert to hex.

----

Hi thanks for the timely reply......

I tried the  first suggestion , but still have the same results....

here is the NSLog output before and after the replaceBytesWith command....

44564456 4944454f 2d564d47 0000020d

and After  

00001bbc 4944454f 2d564d47 0000020d

what i did here was this.... ( this is test code only..... but the results it gives are correctly displayed )

    
int ReplaceMoveData = 7100;


NSMutableData *ScanFile = [NSMutableData dataWithContentsOfFile: @"/MYVOL/THEFILE...."];
NSLog(@"%@",ScanFile);// lets look at the representation of the file ... looks like hex to me!!!!

[ScanFile replaceBytesInRange:NSMakeRange(0,sizeof(ReplaceMoveData)) withBytes:&ReplaceMoveData];
NSLog(@"AFTER REPLACE %@",ScanFile);

 

 ok it looks like while 4456 in the DataObject is only 2 bytes long... the data 7100 that im trying to replace it with is 4 bytes long. == not good.

*Aha! Look carefully at what's changed in the two memory dumps you've provided, and your problem will become clear: you're replacing the **hex** value 0x44564456 with the **decimal** value 7100 (equivalent to the hex value 0x00001bbc, which is what we see in the second dump). So your code should read     short ReplaceMoveData = 0x7100;. If you're creating a hex editor, your real problem is likely to be how to convert whatever values the user types in (likely to be stored as strings) to int values. There's a post above which can give you some pointers*

my intention is to replace the 4456 in beggining of the original data with the Hex representation of 7100. Im basicaly trying to write a simple Hex editor app.
This has got me very stumped !!!

A small point.... If i get the character representations of the hexvalue 71 and 00 and store them in const char , i can then write the correct data to the stream i wish to change.
The compilier spits out a 'NULL value in Literal ' warning , so thats not realy acceptable.


----

thanks guys.... got it all working now.... Thanks again for the shared help.

----

Is there a way to remove bytes from NSMutableData? It seems replaceBytesInRange:withBytes: does not work if you pass it a empty data. Any ideas?

----
Use     - (void)replaceBytesInRange:(NSRange)range withBytes:(const void *)replacementBytes length:(unsigned)replacementLength;.

