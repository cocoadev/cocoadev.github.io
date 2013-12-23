---
layout: page
title: NSScanner
---

Apple's documentation:

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSScanner_Class/Reference/Reference.html


http://developer.apple.com/documentation/Cocoa/Conceptual/Strings/Articles/Scanners.html


----

**Notes:**

from Apple's docs:


"An NSScanner object interprets and converts the characters of an NSString object into number and string values. You assign the scanner's string on creating it, and the scanner progresses through the characters of that string from beginning to end as you request items. This cluster has a single public class, NSScanner." 

Limitations: NSScanners are Objective-C objects only. If you program in Java, use NSFormatter.


----

I've found that NSScanner is *much* faster using strings (e.g. -scanUpToString::) rather than character sets (e.g. scanUpToCharactersFromSet::). as an example, NSScannerTimeTrialSourceCode.

*--boredzo*

If you need something more complex than a character scanner for converting character streams into tokens then consider using Lex (and possibly Yacc) which allow you to construct parsers. Ie, UsingLexAndYaccWithObjectiveC

----

i am trying to use NSScanner to scan some data comming from a serial port. the data is received in this form: "41 0C 0A BE". i am interested in everything after the second pair and converting that to an integer to be displayed in different NSTextFields.  the second set "0C" is a what will be used to identify which textFiled the data should go to. should i even be using NSScanner for this?
so far i have:
    
 NSData *data = [dataDictionary objectForKey:@"data"]; // this data is comming from a NSDictionary where the data read in background by the serial port is stored.
 NSString *str = NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
 NSScanner *scanner = [NSScanner scannerWithString:str];
 unsigned unsignedValue;
 [scanner scanString:@"41" intoString:NULL]; //every transmission from the serial port start with this hex value and then the pid hex value (i.e. "0C") followed by the data
 if ([scanner scanString:@"0C" intoString:NULL]) {
   [scanner scanHexInt: &unsignedValue];
   NSLog(@"Interger value received from data is: %x", &unsignedValue);
   [textField1 insertText: [[NSNumber alloc] initWithUnsignedInt:&unsignedValue;
 }


any ideas why this does not work?

----
The data object you receive, contains the *bytes* 41, 0C, 0A, and BE, not a string "41 0C 0A BE". -initWithData:encoding: will attempt to create a string using the bytes passed to it as if they were encoded with the given encoding, in this case ASCII. Your data does not, however, represent an ASCII string, so the result will not be what you expect.

You can access the bytes in an NSData object directly via the -bytes message; you can then iterate through it as a char array, which will be much faster than turning it into a string:

    
 NSData *data = ...;
 const char *bytes = [data bytes];
 unsigned int length = [data length];
 unsigned int pid;
 
 if (bytes && length > 2 && bytes[0] == 0x41) {
   pid = bytes[1];
   bytes += 2;
   length -= 2;
   
   while (length > 0) {
     
     /* process bytes here */
     
     bytes++;
     length--;
   }
 }


Please note that I have not tested the above code, so I cannot guarantee it works correctly. It should be a good starting point, however.

----

Thanks for the help!  This is definitely heading in the right direction!  i am still having trouble with the last byte of the string.  my results for the following are: 41, c, a, ffffffbe.
why would the last byte have "ffffff" added to the beging of it?

    
 const char dataValue;
 char* ints = "1091308222";
 sscanf(ints, "%u", &dataValue);
 const int dataLength = strlen(&dataValue);
 NSData *data = [NSData dataWithBytes:&dataValue length:dataLength];
 const char *bytes = [data bytes];
 
 if (isxdigit(*bytes) == 0) {
   NSLog(@"the bytes were not a hex string");
 } else { NSLog(@"the bytes are a hex string: %x, %x, %x, %x", bytes[0], bytes[1], bytes[2], bytes[3]); }


----

Use "unsigned char" or "byte" rather than "char" which is by default a signed value. With "char", characters from 0-127 are positive numbers (0x00-0x7F) and 128-255 are negative (-0x80 to -0x01). You often don't notice this signedness using integers because their range is much larger. This discussion doesn't really fit on the NSScanner page anymore does it?

