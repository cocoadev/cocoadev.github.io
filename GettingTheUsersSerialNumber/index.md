---
layout: page
title: GettingTheUsersSerialNumber
---

I'm trying to programmatically get the user's machine serial number.  I'm writing a client program which interacts with one server.  And the user potentially could run the client from many different machines.  I'd like to know when they are connecting from a different machine.  Thanks!

----

Have a look here: http://developer.apple.com/technotes/tn/tn1103.html

It lists a bunch of potentially unique identifiers on a Mac and how to get them. It's a bit old, so a lot of the things may not apply now. It doesn't tell you how to get the machine's serial number, but that's not always reliable anyway. IIRC the serial number is stored somewhere on the hard disk, so a low-level format will destroy it.

*The system serial number is stored in EEPROM on the logic board. When a logic board fails, the serial number should be burned into the replacement, but this doesn't always happen. The end result is that some machines have serial numbers that are missing, blank, all zeroes, or otherwise not-so-unique.*

Is there some reason you can't just generate a UUID and store that in a file somewhere? Do you really need to know if the user is using the same computer but with a different user account, booted to a different partition, or after reinstalling his OS? It's hard to say without knowing what your goal is, but this doesn't sound like the right thing to be doing.

----

You're probably right that's it not the right thing to be doing.  My problem with your technique is that what if they delete the ID file that I store on their computer?

----

What if they do? Since I have no idea *why* you're doing this, I can't possibly comment further. Please explain more about what you're actually trying to do.

Also, although there is a lower barrier for deleting a file, *every* possible technique can be defeated. Serial numbers can be erased, MAC addresses can be changed, etc. etc. It will never be foolproof, although something that doesn't rely on a file will of course be more difficult to defeat.

----

I have one server and n users.  Each user, i, runs the Cocoa client on k_i different machines.  Assume that the job of the client is simply to send a message to the server.  I would like to "stamp" each message sent with some identifier linking to the machine they sent the message from.  The why is that I want to handle the message differently depending on which machine they sent it from.  If I get the machine wrong, then I will handle it incorrectly.

----

Is there a reason the IP address of the client isn't appropriate?  Strikes me as the most effective solution.

----

See CFUUIDGetUUIDBytes. Create a UUID in your install and setup on a new machine.

----

Well, of course the IP can change.  Especially if the client is a laptop.  So I guess there isn't a perfect solution.  What I may end up doing is just put it in the user's hands.  The first time they run their client, I'll ask them to give a name to the machine they're using.  And I'll drop some ID on their computer (in the preferences, perhaps) so in 99.9% of cases they won't be asked again.  But if somehow the file is deleted, I'll just ask them again, also providing a list of choices of all computers they have "registered" with the server.

----

Just like to point out that machines don't always have a serial number (e.g. if the logic board has been replaced)

----

The MAC address should be enough for a unique ID, no? It's guaranteed unique, and it's not like your users will be trying to subvert your system :)

----
There is also some code in
HowToGetHardwareAndNetworkInfo

----

Hey, where'd my edit go? This wiki needs contention management. Anyway...

MACs are not guaranteed unique in the real world, since they can be reassigned in many devices. Also, a Mac can have several MACs (ho ho), and there are no guarantees about the order in which the system will return them.

But from the description of the problem, I think the whole unique-ID thing seems over-engineered. Is it really a huge disaster if, once in a while, your server doesn't recognise the machine and assumes the user has a new one? If not, use the machine's name (as in Sharing) plus a user identifier.

----

Here's a command line tool to get your machine's serial number.

    

/*

serialnum - get the serial number of your machine

See:

- "Uniquely Identifying a Macintosh Computer",
http://developer.apple.com/mac/library/technotes/tn/tn1103.html

- "Re: Moon Travel Planner CFStringRef question" (how to printf a CFStringRef by Ali Ozer),
http://lists.apple.com/archives/carbon-development/2001/Aug/msg01367.html

- http://developer.apple.com/mac/library/DOCUMENTATION/CoreFoundation/Reference/CFStringRef/Reference/reference.html

- http://developer.apple.com/mac/library/documentation/Darwin/Reference/IOKit/IOKitLib_h/index.html#//apple_ref/c/func/IORegistryEntryCreateCFProperty

- http://www.cocoadev.com/index.pl?HowToGetHardwareAndNetworkInfo (alternative implementation, see: + (NSString *)computerSerialNumber)

(- http://wranglingmacs.blogspot.com/2009/04/getting-byhost-string.html)


compile with:

gcc -Wall -O3 -x objective-c -fobjc-exceptions -framework Foundation -framework CoreFoundation -framework IOKit -o serialnum serialnum.c


*/

#import <stdio.h>
#import <Foundation/Foundation.h>
#import <CoreFoundation/CoreFoundation.h>
#include <IOKit/IOKitLib.h>

void CopySerialNumber(CFStringRef *serialNumber);
void show(CFStringRef formatString, ...);


// Returns the serial number as a CFString.
// It is the caller's responsibility to release the returned CFString when done with it.
// taken from: http://developer.apple.com/mac/library/technotes/tn/tn1103.html
void CopySerialNumber(CFStringRef *serialNumber)
{

    if (serialNumber != NULL) {
        *serialNumber = NULL;

        io_service_t    platformExpert = IOServiceGetMatchingService(kIOMasterPortDefault,
                                           IOServiceMatching("IOPlatformExpertDevice"));

        if (platformExpert) {
            CFTypeRef serialNumberAsCFString =
                IORegistryEntryCreateCFProperty(platformExpert,
                                                CFSTR(kIOPlatformSerialNumberKey),
                                                kCFAllocatorDefault, 0);

            if (serialNumberAsCFString) {
                *serialNumber = serialNumberAsCFString;
            }

            IOObjectRelease(platformExpert);
        }
    }
}

// taken from: http://lists.apple.com/archives/carbon-development/2001/Aug/msg01367.html
// see also: /Developer/Examples/CoreFoundation/String/StringExample.c    (simple CFString example program by Ali Ozer)
void show(CFStringRef formatString, ...) {
   CFStringRef resultString;
   CFDataRef data;
   va_list argList;
   va_start(argList, formatString);
   resultString = CFStringCreateWithFormatAndArguments(NULL, NULL, formatString, argList);
   va_end(argList);
   data = CFStringCreateExternalRepresentation(NULL, resultString, 
   CFStringGetSystemEncoding(), '?');
   if (data != NULL) {
      printf ("%.*s\n", (int)CFDataGetLength(data), CFDataGetBytePtr(data));
      CFRelease(data);
   }
   CFRelease(resultString);
}

int main(void)
{

   CFStringRef serialNumber;   
   CopySerialNumber(&serialNumber);
   show(CFSTR("%@"), serialNumber);
   CFRelease(serialNumber);

/*
   // without calling show() function
   NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
   CFStringRef serialNumber; 
   CopySerialNumber(&serialNumber); 
   printf("%s\n", [[NSString stringWithFormat: @"%@", serialNumber] UTF8String]);
   CFRelease(serialNumber);
   [pool release];
*/

   return 0;
}



----

    

/*

serialnum - get the serial number of your machine

Adapted from:

- http://wranglingmacs.blogspot.com/2009/04/getting-byhost-string.html (without using CFStringRef)


compile with:

gcc -Wall -O3 -x objective-c -fobjc-exceptions -framework Foundation -framework CoreFoundation -framework IOKit -o serialnum serialnum.c


*/

#import <stdio.h>
#import <Foundation/Foundation.h>
#import <CoreFoundation/CoreFoundation.h>
#include <IOKit/IOKitLib.h>


int main(void)
{

   NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

   NSString *serialNumberString = nil;

   io_struct_inband_t iokit_entry;

   uint32_t bufferSize = 4096; // this signals the longest entry we will take

   io_registry_entry_t ioRegistryRoot = IORegistryEntryFromPath(kIOMasterPortDefault, "IOService:/");

   IORegistryEntryGetProperty(ioRegistryRoot, kIOPlatformSerialNumberKey, iokit_entry, &bufferSize);

   serialNumberString = [NSString stringWithCString: iokit_entry encoding: NSASCIIStringEncoding];

   IOObjectRelease((unsigned int) iokit_entry);

   IOObjectRelease(ioRegistryRoot);

   printf("%s\n", [serialNumberString UTF8String]);

   [pool release];

   return 0;
}


