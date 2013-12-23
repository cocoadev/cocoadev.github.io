---
layout: page
title: HowToGetHardwareAndNetworkInfo
---

 <-- is that OK??


Nobody helped me (see the Original Post below). Snif. But I figured it out by myself. What finally got me started was the addition of 'Carbon' in my googles searches (I had tried several times before with no luck).

Of course, I still put the code here with links to where I found the code (see The Solution below). Please feel free to add more in the code section...  

--CharlesParnot

----
**Original post - nobody helped me  )`:**

I am trying to get some basic profile of the machine running my Cocoa program, in particular: http://goo.gl/OeSCu

* the  number of processors
* the clock speed of the processor(s)
* the CPU type
* the hostname and or ip address
* the RendezVous name (as set in the Sharing pref pane)


So I want something like a light system profile.
I have found some info out there and have some working code for the number of processors, and the type of processor (see GettingTheProcessor). I can get the hostnames through NSHost. The rest, I don't know! I looked hard, and could not find anything obvious. Even the RendezVous thing is quite obscure It seems IOKit / IORegistry could help for some of that. Useful links eith some tutorials/example would also help. Maybe somebody has written some Cocoa wrapper for all that stuff.

Thanks for any help you can provide. I will put all the code I can get to work back here, of course.

CharlesParnot

----
**The Solution**

Dont' forget in Xcode: it seems you have to link against the Carbon, SystemConfiguration and IOKit frameworks. 

    

**CPSystemInformation.h**

 @interface CPSystemInformation : NSObject {}
  
 //all the info at once!
 + (NSDictionary *)miniSystemProfile;
  
 + (NSString *)machineType;
 + (NSString *)humanMachineType;
 + (NSString *)humanMachineTypeAlternate;
  
 + (long)processorClockSpeed;
 + (long)processorClockSpeedInMHz;
 + (unsigned int)countProcessors;
 + (BOOL) isPowerPC;
 + (BOOL) isG3;
 + (BOOL) isG4;
 + (BOOL) isG5;
 + (NSString *)powerPCTypeString;
  
 + (NSString *)computerName;
 + (NSString *)computerSerialNumber;
  
 + (NSString *)operatingSystemString;
 + (NSString *)systemVersionString;
  


    

**CPSystemInformation.m**

 #import "CPSystemInformation.h"
 
 #import <Carbon/Carbon.h>
 #import <SystemConfiguration/SystemConfiguration.h>
 
 @implementation CPSystemInformation
 
 //get everything!
 + (NSDictionary *)miniSystemProfile
 {
 	return [NSDictionary dictionaryWithObjectsAndKeys:
 		[self machineType],@"MachineType",
 		[self humanMachineType],@"HumanMachineType",
 		[self powerPCTypeString],@"ProcessorType",
 		[NSNumber numberWithLong:
                         [self processorClockSpeed]],
                             @"ProcessorClockSpeed",
 		[NSNumber numberWithLong:
                         [self processorClockSpeedInMHz]],
                             @"ProcessorClockSpeedInMHz",
 		[NSNumber numberWithInt:[self countProcessors]],
                         @"CountProcessors",
 		[self computerName],@"ComputerName",
 		[self computerSerialNumber],@"ComputerSerialNumber",
 		[self operatingSystemString],@"OperatingSystem",
 		[self systemVersionString],@"SystemVersion",		
 		nil];
 }
 
 
 #pragma mark *** Getting the Human Name for the Machine Type ***
 
 /* adapted from http://nilzero.com/cgi-bin/mt-comments.cgi?entry_id=1300 */
 /*see below 'humanMachineNameFromNilZeroCom()' for the original code */
 /*this code used a dictionary insted - see 'translationDictionary()' below */
 
 //non-human readable machine type/model
 + (NSString *)machineType
 {
 	OSErr err;
 	char *machineName=NULL;    // This is really a Pascal-string with a length byte.
         //gestaltUserVisibleMachineName = 'mnam'
 	err = Gestalt(gestaltUserVisibleMachineName, (long*) &machineName);
 	if( err== noErr )
 		return [NSString stringWithCString: machineName +1 length: machineName[0]];
 	else
 		return @"machineType: machine name cannot be determined";
 }
 
 //dictionary used to make the machine type human-readable
 static NSDictionary *translationDictionary=nil;
 + (NSDictionary *)translationDictionary
 {
 	if (translationDictionary==nil)
 		translationDictionary=[[NSDictionary alloc] initWithObjectsAndKeys:
 			@"PowerMac 8500/8600",@"AAPL,8500",
 			@"PowerMac 9500/9600",@"AAPL,9500",
 			@"PowerMac 7200",@"AAPL,7200",
 			@"PowerMac 7200/7300",@"AAPL,7300",
 			@"PowerMac 7500",@"AAPL,7500",
 			@"Apple Network Server",@"AAPL,ShinerESB",
 			@"Alchemy(Performa 6400 logic-board design)",@"AAPL,e407",
 			@"Gazelle(5500)",@"AAPL,e411",
 			@"PowerBook 3400",@"AAPL,3400/2400",
 			@"PowerBook 3500",@"AAPL,3500",
 			@"PowerMac G3 (Gossamer)",@"AAPL,Gossamer",
 			@"PowerMac G3 (Silk)",@"AAPL,PowerMac G3",
 			@"PowerBook G3 (Wallstreet)",@"AAPL,PowerBook1998",
 			@"Yikes! Old machine - unknown model",@"AAPL",
 			
 			@"iMac (first generation)",@"iMac,1",
 			@"iMac (first generation) - unknown model",@"iMac",
 			
 			@"PowerBook G3 (Lombard)",@"PowerBook1,1",
 			@"iBook (clamshell)",@"PowerBook2,1",
 			@"iBook FireWire (clamshell)",@"PowerBook2,2",
 			@"PowerBook G3 (Pismo)",@"PowerBook3,1",
 			@"PowerBook G4 (Titanium)",@"PowerBook3,2",
 			@"PowerBook G4 (Titanium w/ Gigabit Ethernet)",
 						@"PowerBook3,3",
 			@"PowerBook G4 (Titanium w/ DVI)",@"PowerBook3,4",
 			@"PowerBook G4 (Titanium 1GHZ)",@"PowerBook3,5",
 			@"iBook (12in May 2001)",@"PowerBook4,1",
 			@"iBook (May 2002)",@"PowerBook4,2",
 			@"iBook 2 rev. 2 (w/ or w/o 14in LCD) (Nov 2002)",
 						@"PowerBook4,3",
 			@"iBook 2 (w/ or w/o 14in LDC)",@"PowerBook4,4",
 			@"PowerBook G4 (Aluminum 17in)",@"PowerBook5,1",
 			@"PowerBook G4 (Aluminum 15in)",@"PowerBook5,2",
 			@"PowerBook G4 (Aluminum 17in rev. 2)",@"PowerBook5,3",
 			@"PowerBook G4 (Aluminum 12in)",@"PowerBook6,1",
 			@"PowerBook G4 (Aluminum 12in)",@"PowerBook6,2",
 			@"iBook G4",@"PowerBook6,3",
 			@"PowerBook or iBook - unknown model",@"PowerBook",
 			
 			@"Blue & White G3",@"PowerMac1,1",
 			@"PowerMac G4 PCI Graphics",@"PowerMac1,2",
 			@"iMac FireWire (CRT)",@"PowerMac2,1",
 			@"iMac FireWire (CRT)",@"PowerMac2,2",
 			@"PowerMac G4 AGP Graphics",@"PowerMac3,1",
 			@"PowerMac G4 AGP Graphics",@"PowerMac3,2",
 			@"PowerMac G4 AGP Graphics",@"PowerMac3,3",
 			@"PowerMac G4 (QuickSilver)",@"PowerMac3,4",
 			@"PowerMac G4 (QuickSilver)",@"PowerMac3,5",
 			@"PowerMac G4 (MDD/Windtunnel)",@"PowerMac3,6",
 			@"iMac (Flower Power)",@"PowerMac4,1",
 			@"iMac (Flat Panel 15in)",@"PowerMac4,2",
 			@"eMac",@"PowerMac4,4",
 			@"iMac (Flat Panel 17in)",@"PowerMac4,5",
 			@"PowerMac G4 Cube",@"PowerMac5,1",
 			@"PowerMac G4 Cube",@"PowerMac5,2",
 			@"iMac (Flat Panel 17in)",@"PowerMac6,1",
 			@"PowerMac G5",@"PowerMac7,2",
 			@"PowerMac G5",@"PowerMac7,3",
 			@"PowerMac - unknown model",@"PowerMac",
 			
 			@"XServe",@"RackMac1,1",
 			@"XServe rev. 2",@"RackMac1,2",
 			@"XServe G5",@"RackMac3,1",
 			@"XServe - unknown model",@"RackMac",
 			
 			@"Mac Mini",@"Macmini1,1",	// Seen on a 1st gen. Core Duo, but may also be on others...?
 			@"Mac Mini - unknown model",@"Macmini",
 
 			nil];
 	return translationDictionary;
 }
 
 + (NSString *)humanMachineType
 {
 	NSString *human=nil;
 	NSString *machineType;
 
 	machineType=[self machineType];
 	
 	//return the corresponding entry in the NSDictionary
 	NSDictionary *translation=[self translationDictionary];
 	NSString *aKey;
 	//keys should be sorted to distinguish 'generic' from 'specific' names
 	NSEnumerator *e=[translation allKeys]
 					sortedArrayUsingSelector:@selector(compare:)]
 						objectEnumerator];
 	[[NSRange r;
 	while (aKey=[e nextObject]) {
 		r=[machineType rangeOfString:aKey];
 		if (r.location!=NSNotFound)
 			//continue searching : the first hit will be the generic name
 			human=[translation objectForKey:aKey];
 	}
 	if (human)
 		return human;
 	else
 		return machineType;
 }
 
 //for some reason, this does not work
 //probably old stuff still around
 + (NSString *)humanMachineTypeAlternate
 {
 	OSErr err;
 	long result;
 	Str255 name;
 	err=Gestalt('mach',&result); //gestaltMachineType = 'mach'
 	if (err==nil) {
 		GetIndString(name,kMachineNameStrID,(short)result);
 		return [NSString stringWithCString:name];
 	} else
 		return @"humanMachineTypeAlternate: machine name cannot be determined";
 }
 
 
 #pragma mark *** Getting Processor info ***
 
 + (long)processorClockSpeed
 {
 	OSErr err;
 	long result;
 	err=Gestalt(gestaltProcClkSpeed,&result);
 	if (err!=nil)
 		return 0;
 	else
 		return result;
 }
 
 + (long)processorClockSpeedInMHz
 {
 	return [self processorClockSpeed]/1000000;
 }
 
 #include <mach/mach_host.h>
 #include <mach/host_info.h>
 + (unsigned int)countProcessors
 {
 	host_basic_info_data_t hostInfo;
 	mach_msg_type_number_t infoCount;
 	
 	infoCount = HOST_BASIC_INFO_COUNT;
 	host_info(mach_host_self(), HOST_BASIC_INFO, 
 			  (host_info_t)&hostInfo, &infoCount);
 	
 	return (unsigned int)(hostInfo.max_cpus);
 	
 }
 
 #include <mach/mach.h>
 #include <mach/machine.h>
 
 
 // the following methods were more or less copied from
 //	http://developer.apple.com/technotes/tn/tn2086.html
 //	http://www.cocoadev.com/index.pl?GettingTheProcessor
 //	and can be better understood with a look at
 //	file:///usr/include/mach/machine.h
 
 + (BOOL) isPowerPC
 {
 	host_basic_info_data_t hostInfo;
 	mach_msg_type_number_t infoCount;
 	
 	infoCount = HOST_BASIC_INFO_COUNT;
 	kern_return_t ret = host_info(mach_host_self(), HOST_BASIC_INFO,
 					(host_info_t)&hostInfo, &infoCount);
 	
 	return ( (KERN_SUCCESS == ret) &&
 			(hostInfo.cpu_type == CPU_TYPE_POWERPC) );
 }
 
 + (BOOL) isG3
 {
 	host_basic_info_data_t hostInfo;
 	mach_msg_type_number_t infoCount;
 	
 	infoCount = HOST_BASIC_INFO_COUNT;
 	kern_return_t ret = host_info(mach_host_self(), HOST_BASIC_INFO,
 						(host_info_t)&hostInfo, &infoCount);
 	
 	return ( (KERN_SUCCESS == ret) &&
 			 (hostInfo.cpu_type == CPU_TYPE_POWERPC) &&
 			 (hostInfo.cpu_subtype == CPU_SUBTYPE_POWERPC_750) );
 }
 
 + (BOOL) isG4
 {
 	host_basic_info_data_t hostInfo;
 	mach_msg_type_number_t infoCount;
 	
 	infoCount = HOST_BASIC_INFO_COUNT;
 	kern_return_t ret = host_info(mach_host_self(), HOST_BASIC_INFO,
 						(host_info_t)&hostInfo, &infoCount);
 	
 	return ( (KERN_SUCCESS == ret) &&
 			 (hostInfo.cpu_type == CPU_TYPE_POWERPC) &&
 			 (hostInfo.cpu_subtype == CPU_SUBTYPE_POWERPC_7400 ||
 			  hostInfo.cpu_subtype == CPU_SUBTYPE_POWERPC_7450));
 }
 
 #ifndef CPU_SUBTYPE_POWERPC_970
 #define CPU_SUBTYPE_POWERPC_970 ((cpu_subtype_t) 100)
 #endif
 + (BOOL) isG5
 {
 	host_basic_info_data_t hostInfo;
 	mach_msg_type_number_t infoCount;
 	
 	infoCount = HOST_BASIC_INFO_COUNT;
 	kern_return_t ret = host_info(mach_host_self(), HOST_BASIC_INFO,
 						(host_info_t)&hostInfo, &infoCount);
 	
 	return ( (KERN_SUCCESS == ret) &&
 			 (hostInfo.cpu_type == CPU_TYPE_POWERPC) &&
 			 (hostInfo.cpu_subtype == CPU_SUBTYPE_POWERPC_970));
 }	
 
 + (NSString *)powerPCTypeString
 {
 	if ([self isG3])
 		return @"G3";
 	else if ([self isG4])
 		return @"G4";
 	else if ([self isG5])
 		return @"G5";
 	else if ([self isPowerPC])
 		return @"PowerPC pre-G3";
 	else
 		return @"Non-PowerPC";
 
 }
 
 #pragma mark *** Machine information ***
 
 //this used to be called 'Rendezvous name' (X.2), now just 'Computer name' (X.3)
 //see here for why: http://developer.apple.com/qa/qa2001/qa1228.html
 //this is the name set in the Sharing pref pane
 + (NSString *)computerName
 {
 	CFStringRef name;
 	NSString *computerName;
 	name=SCDynamicStoreCopyComputerName(NULL,NULL);
 	computerName=[NSString stringWithString:(NSString *)name];
 	CFRelease(name);
 	return computerName;
 }
 
 /* copied from http://cocoa.mamasam.com/COCOADEV/2003/07/1/68334.php */
 /* and modified by http://nilzero.com/cgi-bin/mt-comments.cgi?entry_id=1300 */
 /* and by http://cocoa.mamasam.com/COCOADEV/2003/07/1/68337.php/ */
 + (NSString *)computerSerialNumber
 {
 	NSString         *result = @"";
 	mach_port_t       masterPort;
 	kern_return_t      kr = noErr;
 	io_registry_entry_t  entry;    
 	CFDataRef         propData;
 	CFTypeRef         prop;
 	CFTypeID         propID=NULL;
 	UInt8           *data;
 	unsigned int        i, bufSize;
 	char            *s, *t;
 	char            firstPart[64], secondPart[64];
 	
 	kr = IOMasterPort(MACH_PORT_NULL, &masterPort);        
 	if (kr == noErr) {
 		entry = IORegistryGetRootEntry(masterPort);
 		if (entry != MACH_PORT_NULL) {
 			prop = IORegistryEntrySearchCFProperty(entry,
 						kIODeviceTreePlane,
 						CFSTR("serial-number"),
                                         nil, kIORegistryIterateRecursively);
 			if (prop == nil) {
 				result = @"null";
 			} else {
 				propID = CFGetTypeID(prop);
 			}
 			if (propID == CFDataGetTypeID()) {
 				propData = (CFDataRef)prop;
 				bufSize = CFDataGetLength(propData);
 				if (bufSize > 0) {
 					data = CFDataGetBytePtr(propData);
 					if (data) {
 						i = 0;
 						s = data;
 						t = firstPart;
 						while (i < bufSize) {
 							i++;
 							if (*s != '\0') {
 								*t++ = *s++;
 							} else {
 								break;
 							}
 						}
 						*t = '\0';
 						
 						while ((i < bufSize) && (*s == '\0')) {
 							i++;
 							s++;
 						}
 						
 						t = secondPart;
 						while (i < bufSize) {
 							i++;
 							if (*s != '\0') {
 								*t++ = *s++;
 							} else {
 								break;
 							}
 						}
 						*t = '\0';
 						result =
 						[NSString stringWithFormat:
 						 @"%s%s",secondPart,firstPart];
 					}
 				}
 			}
 		}
 		mach_port_deallocate(mach_task_self(), masterPort);
 	}
 	return(result);
 }
 
 #pragma mark *** System version ***
 
 + (NSString *)operatingSystemString
 {
 	NSProcessInfo *procInfo = [NSProcessInfo processInfo];
 	return [procInfo operatingSystemName];
 }
 
 + (NSString *)systemVersionString
 {
 	NSProcessInfo *procInfo = [NSProcessInfo processInfo];
 	return [procInfo operatingSystemVersionString];
 }
 
 @end
 


----

Some more code from when this topic came up on Cocoa-Dev:

The first gets you the physical RAM size in MBs. You could also get it in bytes, but since Gestalt returns a signed 32-bit number, this would not be able to return any RAM above 2GB, which is an amount that increasingly more and more Macs these days seem to have.

    

unsigned	UKPhysicalRAMSize()	// In MBs.
{
	long		ramSize;

	if( Gestalt( gestaltPhysicalRAMSizeInMegabytes, &ramSize ) == noErr )
		return ramSize;
	else
		return 0;
}



And here's another approach to get the system version:

    

NSString*	UKSystemVersion()
{
	long		sysVersion;

	if( Gestalt( gestaltSystemVersion, &sysVersion ) != noErr )
		return nil;

	int		majorHiNib, majorLoNib, minorNib, bugNib;

	majorHiNib = (sysVersion & 0x0000F000) >> 12;
	majorLoNib = (sysVersion & 0x00000F00) >> 8;
	minorNib = (sysVersion & 0x000000F0) >> 4;
	bugNib = sysVersion & 0x0000000F;

	if( majorHiNib == 0 )
		return [NSString stringWithFormat: @"%ld.%ld.%ld", majorLoNib, minorNib, bugNib];
	else
		return [NSString stringWithFormat: @"%ld%ld.%ld.%ld", majorHiNib, majorLoNib, minorNib, bugNib];
}



This is also a nice example on how to decode BCD numbers as Carbon uses them to store version numbers. It's unlikely you'll ever hit the case where you have a one-digit number in front of the system version, but maybe if your app parses 'vers' resources or gets some other version number in BCD you'll want to borrow this code.

Oh, and finally, here's a variation of the serial number getting code. I think my version is a little more readable:

    

NSString*	UKSystemSerialNumber()
{
	mach_port_t				masterPort;
	kern_return_t			kr = noErr;
	io_registry_entry_t		entry;
	CFTypeRef				prop;
	CFTypeID				propID;
	NSString*				str = nil;

	kr = IOMasterPort(MACH_PORT_NULL, &masterPort);
	if( kr != noErr )
		goto cleanup;
	entry = IORegistryGetRootEntry( masterPort );
	if( entry == MACH_PORT_NULL )
		goto cleanup;
	prop = IORegistryEntrySearchCFProperty(entry, kIODeviceTreePlane, CFSTR("serial-number"), nil, kIORegistryIterateRecursively);
	if( prop == nil )
		goto cleanup;
	propID = CFGetTypeID( prop );
	if( propID != CFDataGetTypeID() )
		goto cleanup;
	
	const char*	buf = [(NSData*)prop bytes];
	int			len = [(NSData*)prop length],
				 x;
	
	char	secondPart[256];
	char	firstPart[256];
	char*	currStr = secondPart; // Version number starts with second part, then NULLs, then first part.
	int		y = 0;
	
	for( x = 0; x < len; x++ )
	{
		if( buf[x] > 0 && (y < 255) )
			currStr[y++] = buf[x];
		else if( currStr == secondPart )
		{
			currStr[y] = 0;		// Terminate string.
			currStr = firstPart;
			y = 0;
		}
	}
	currStr[y] = 0;	// Terminate string.
	
	str = [NSString stringWithFormat: @"%s%s", firstPart, secondPart];
	
cleanup:
	mach_port_deallocate( mach_task_self(), masterPort );
	
	return str;
}



----

The above serial number code produced incorrect results on my new MacBook, so I did some searching.
Starting with Panther, the serial number is available in the IOPlatformSerialNumber property of the the IOPlatformExpertDevice node in the I/O registry.
See:    http://developer.apple.com/technotes/tn/tn1103.html
  -DanTreiman

Do you know <sys/sysctl.h> ? It has some features to get various informations about the installed hardware and is (afaik) portable (runs not only on Darwin).
For an example see the manpage by Apple:  http://developer.apple.com/documentation/Darwin/Reference/ManPages/man3/sysctlbyname.3.html
Another example shows, how to get the physical RAM of your machine:

    

#import <sys/sysctl.h>

#define GB (1024*1024*1024)        // 1 Gigabyte of RAM


int error = 0 ;
int value = 0 ;
unsigned long length = sizeof(value) ;

int selection[2] = { CTL_HW, HW_PHYSMEM } ;


error = sysctl(selection, 2, &value, &length, NULL, 0) ;

if ( error == 0 )
{
   NSLog(@"Your machine has %i Bytes of RAM (%f GB)", value, (value / GB)) ;
}
else NSLog(@"sysctl error occured!") ;





LorenzHipp

----

Anyone got a function for disk space or remaining disk space?

~J

getmntinfo(3), getfsstat(2) and statfs(2) will give you information about mounted file systems.

----

I had to fix the +machineType method. It was buggy. For one, gestaltUserVisibleMachineName returns a Pascal string, so it may not be zero-terminated, and there's a length byte at the start. I'm surprised the original code even worked, because the dictionary couldn't possibly match on the string with the length byte at the start.

Also, the code was comparing an OSErr against NIL, which isn't good style, so I changed that to noErr for correctness' sake.

Finally, I added the Mac Mini, because that's what I'm running on. Anyone have other Mac names? This seems to be missing a huge bunch of the Intel Macs...

-- UliKusterer

----

gestaltUserVisibleMachineName seems to be undefined on Mac OS X 10.5 in 64-bit mode (Gestalt() returns gestaltUndefSelectorErr). Is anyone aware of a 64-bit-safe replacement?
----
Oh, of course. sysctlbyname() works:
    
char modelBuffer[256];
size_t sz = sizeof(modelBuffer);
if (0 == sysctlbyname("hw.model", modelBuffer, &sz, NULL, 0)) {
	modelBuffer[sizeof(modelBuffer) - 1] = 0;
	printf("%s", modelBuffer);
}


*A (slightly) more orderly and up-to-date list of Mac names is found at MacintoshModels.*

----
Same solution as previous, but avoiding a fixed buffer.
    
// Return an autoreleased string containing this system's model name,
// e.g., "MacBookPro5,5", or nil if we were unable to determine the model
// name.

NSString *
GetModelString()
{
    NSString * modelString  = nil;
    int        modelInfo[2] = { CTL_HW, HW_MODEL };
    size_t     modelSize;

    if (sysctl(modelInfo,
               2,
               NULL,
               &modelSize,
               NULL, 0) == 0)
    {
        void * modelData = malloc(modelSize);
        
        if (modelData)
        {
            if (sysctl(modelInfo,
                       2,
                       modelData,
                       &modelSize,
                       NULL, 0) == 0)
            {
                modelString = [NSString stringWithUTF8String:modelData];
            }
            
            free(modelData);
        }
    }
    
    return modelString;
}



----
In order to get the machine name I use this format:
    
    OSErr err;
	StringPtr machineNamePascal = NULL;
	err = Gestalt(gestaltUserVisibleMachineName, (SInt32 *)&machineNamePascal);
    NSString *machineName;
	if(err == noErr)
        machineName = [(NSString *)CFStringCreateWithPascalString(NULL,
                                                                  machineNamePascal,
                                                                  NSMacOSRomanStringEncoding)
                       autorelease];
  

Can anyone tell me why I don't need to free machineNamePascal?

Thanks,

Avi

----

Yet another interesting C snippet showing how to retrieve hardware information on Mac OS X.

    

/*

C snippet posted by Guiyon on Jun 12, 2009, 09:38 AM at:

"Getting Hardware Details from Objective-C",
http://forums.macrumors.com/showthread.php?t=718118

compile with:

gcc -Wall -O3 -x objective-c -fobjc-exceptions -fnested-functions -framework Foundation \
    -framework CoreFoundation -framework IOKit -o machardwareinfo machardwareinfo.c

./machardwareinfo

Should run on Mac OS X 10.4 and later.

# IOCPUID
ioreg -l -w0 | awk -F'= ' '/"IOCPUID" = /{print $2; exit}'
ioreg -l -w0 | grep 'device_type' | grep -E -i 'cpu|processor'


See also:

- http://www.cocoadev.com/index.pl?IOKit (get MAC address snippets)
- http://www.cocoadev.com/index.pl?GettingTheUsersSerialNumber
- http://www.cocoadev.com/index.pl?HowToGetHardwareAndNetworkInfo
- http://wranglingmacs.blogspot.com/2009/04/getting-byhost-string.html

*/

#import <Foundation/Foundation.h>
#import <IOKit/IOKitLib.h>

#include <sys/sysctl.h>
#include <sys/resource.h>
#include <sys/vm.h>


/* ADDED !!! */
#ifndef MAC_OS_X_VERSION_10_4
#define MAC_OS_X_VERSION_10_4 1040
#endif

#if (MAC_OS_X_VERSION_MAX_ALLOWED > MAC_OS_X_VERSION_10_4)
#define MAC_OS_X_VERSION_10_5_AND_LATER 1
#define MATCHSTR "processor"
#else
#define MAC_OS_X_VERSION_10_5_AND_LATER 0
#define MATCHSTR "cpu"
#endif

#ifndef KERN_OSVERSION
//#define KERN_OSVERSION 8110
#define KERN_OSVERSION "Darwin"
#endif


static void objc_println( NSString* format, ... );
static void printSysctlInfo( void );
static NSDictionary* getCpuIds( void );
static NSString* getPlatformSerialNumber( void );

int main (int argc, const char * argv[]) {
  NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
  
  objc_println(@"Beginning system data collection...");
  
  NSProcessInfo* pinfo = [NSProcessInfo processInfo];
  
  objc_println( @"NSProcessInfo:" );
  objc_println( @"\tProcess Name: %@", [pinfo processName] );
  objc_println( @"\tPID: %d", [pinfo processIdentifier] );
  objc_println( @"\tProcess GUID: %@", [pinfo globallyUniqueString] );
  objc_println( @"\tOS Version: %@", [pinfo operatingSystemVersionString] );
  if (MAC_OS_X_VERSION_10_5_AND_LATER)      /* ADDED !!! */
  {
     objc_println( @"\tTotal Processors: %d", [pinfo processorCount] );
     objc_println( @"\tActive Processors: %d", [pinfo activeProcessorCount] );
     objc_println( @"\tTotal RAM: %ull bytes", [pinfo physicalMemory] );
  }else {
     objc_println( @"\tTotal Processors: [not available]");
     objc_println( @"\tActive Processors: [not available]");
     objc_println( @"\tTotal RAM: XXX bytes [not available]");
  }
  
  printSysctlInfo();
  
  objc_println( @"IOKit:" );
  NSDictionary *cpuInfo = getCpuIds();
  NSArray* keys = cpuInfo allKeys] sortedArrayUsingSelector:@selector(caseInsensitiveCompare:)];
 
/* 
  for( id key in keys ) {
    objc_println( @"\t%@ has ID: 0x%8.8x", key, [([[NSNumber*)[cpuInfo objectForKey: key] unsignedIntValue] );
  }
*/

  /* ADDED !!! */
  /* more portable code than: for( id key in keys ) { ... */
  int i, num;
  num = [keys count];
  for (i = 0; i < num; i++) 
  {
     objc_println( @"\t%@ has CPU ID:\n\tdec: %d\n\thex: 0x%8.8x", [keys objectAtIndex: i], \
        [(NSNumber*)[cpuInfo objectForKey: [keys objectAtIndex: i]] unsignedIntValue],
        [(NSNumber*)[cpuInfo objectForKey: [keys objectAtIndex: i]] unsignedIntValue] );
  }

  objc_println( @"\tSerial Number: %@", getPlatformSerialNumber() );
  
  /* [pool drain]; */
  [pool release];
  
  return 0;
}

static void objc_println( NSString* format, ... ) {
  va_list args;

  if (![format hasSuffix: @"\n"]) {
    format = [format stringByAppendingString: @"\n"];
  }
	
  va_start (args, format);
  NSString *body =  [[NSString alloc] initWithFormat: format
                                           arguments: args];
  va_end (args);
	
  fprintf(stderr,"%s",[body UTF8String]);
  
  [body release];
}

static void printSysctlInfo( void ) {
  objc_println( @"sysctl:" );
  
  int mib[2];
  size_t len = 0;
  char *rstring = NULL;
  unsigned int rint = 0;
  
  /* Setup the MIB data */
  mib[0] = CTL_KERN;
  mib[1] = KERN_OSTYPE;
  
  /* Get the length of the string */
  sysctl( mib, 2, NULL, &len, NULL, 0 );
  
  /* Now allocate space for the string and grab it */
  rstring = malloc( len );
  sysctl( mib, 2, rstring, &len, NULL, 0 );
  objc_println( @"\tkern.ostype: %s", rstring );
  
  /* Make sure we clean up afterwards! */
  free( rstring );
  rstring = NULL;
  
  /* Get the kernel release number */
  mib[0] = CTL_KERN;
  mib[1] = KERN_OSRELEASE;
  sysctl( mib, 2, NULL, &len, NULL, 0 );
  rstring = malloc( len );
  sysctl( mib, 2, rstring, &len, NULL, 0 );
  objc_println( @"\tkern.osrelease: %s", rstring );
  free( rstring );
  rstring = NULL;
  
  /* Get the Mac OS X Build number */
  mib[0] = CTL_KERN;
  mib[1] = KERN_OSVERSION;
  sysctl( mib, 2, NULL, &len, NULL, 0 );
  rstring = malloc( len );
  sysctl( mib, 2, rstring, &len, NULL, 0 );
  objc_println( @"\tkern.osversion: %s", rstring );
  free( rstring );
  rstring = NULL;
  
  mib[0] = CTL_HW;
  mib[1] = HW_MODEL;
  sysctl( mib, 2, NULL, &len, NULL, 0 );
  rstring = malloc( len );
  sysctl( mib, 2, rstring, &len, NULL, 0 );
  objc_println( @"\thw.model: %s", rstring );
  free( rstring );
  rstring = NULL;
  
  sysctlbyname( "machdep.cpu.brand_string", NULL, &len, NULL, 0 );
  rstring = malloc( len );
  sysctlbyname( "machdep.cpu.brand_string", rstring, &len, NULL, 0 );
  objc_println( @"\tmachdep.cpu.brand_string: %s", rstring );
  free( rstring );
  rstring = NULL;
  
  mib[0] = CTL_HW;
  mib[1] = HW_CPU_FREQ;
  len = sizeof( rint );
  sysctl( mib, 2, &rint, &len, NULL, 0 );
  objc_println( @"\thw.cpufrequency: %u", rint );
}

static NSString* getPlatformSerialNumber( void ) {
  io_registry_entry_t     rootEntry = IORegistryEntryFromPath( kIOMasterPortDefault, "IOService:/" );
  CFTypeRef serialAsCFString = NULL;
  
  serialAsCFString = IORegistryEntryCreateCFProperty( rootEntry,
                                                     CFSTR(kIOPlatformSerialNumberKey),
                                                     kCFAllocatorDefault,
                                                     0);

  IOObjectRelease( rootEntry );
  return (NULL != serialAsCFString) ? [(NSString*)serialAsCFString autorelease] : nil;
}

static NSDictionary* getCpuIds( void ) {
  NSMutableDictionary*    cpuInfo = [[NSMutableDictionary alloc] init];
  CFMutableDictionaryRef  matchClasses = NULL;
  kern_return_t           kernResult = KERN_FAILURE;
  mach_port_t             machPort;
  io_iterator_t           serviceIterator;
  
  io_object_t             cpuService;
  
  kernResult = IOMasterPort( MACH_PORT_NULL, &machPort );
  if( KERN_SUCCESS != kernResult ) {
    printf( "IOMasterPort failed: %d\n", kernResult );
  }
  

  /* ADDED !!! */
  matchClasses = IOServiceNameMatching( MATCHSTR );
  /* matchClasses = IOServiceNameMatching( "processor" ); */


  if( NULL == matchClasses ) {
    printf( "IOServiceMatching returned a NULL dictionary" );
  }
  
  kernResult = IOServiceGetMatchingServices( machPort, matchClasses, &serviceIterator );
  if( KERN_SUCCESS != kernResult ) {
    printf( "IOServiceGetMatchingServices failed: %d\n", kernResult );
  }
  
  while( (cpuService = IOIteratorNext( serviceIterator )) ) {
    CFTypeRef CPUIDAsCFNumber = NULL;
    io_name_t nameString;      
    IORegistryEntryGetNameInPlane( cpuService, kIOServicePlane, nameString );
    
    CPUIDAsCFNumber = IORegistryEntrySearchCFProperty( cpuService, 
                                                      kIOServicePlane,
                                                      CFSTR( "IOCPUID" ), 
                                                      kCFAllocatorDefault,
                                                      kIORegistryIterateRecursively);
    
    if( NULL != CPUIDAsCFNumber ) {
      NSString* cpuName = [NSString stringWithCString:nameString];
      [cpuInfo setObject:(NSNumber*)CPUIDAsCFNumber forKey:cpuName];
    }
    
    if( NULL != CPUIDAsCFNumber ) {
      CFRelease( CPUIDAsCFNumber );
    }
  }
  
  IOObjectRelease( serviceIterator );
  
  return [cpuInfo autorelease];
}



----

Note that you can make use of the **system_profiler** command line to gather full information about the system. Its **-xml** option generates a plist file that can be used easily in any Cocoa application:

    
     system_profiler reports on the hardware and software configuration of the system.  It can generate plain text reports or XML reports which can be opened with SystemProfiler.app

     -xml                Generates a report in XML format.  If the XML report is redirected to a file with a ".spx" suffix that file can be opened with System Profiler.app.

     -listDataTypes      Lists the available datatypes.

     -detailLevel level  Specifies the level of detail for the report:

                         mini          report with no personal information

                         basic         basic hardware and network information

                         full          all available information

