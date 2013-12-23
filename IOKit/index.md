---
layout: page
title: IOKit
---



IOKit provides access to devices. It is an object-oriented framework leveraged by programmers for creating device drivers. It uses a subset of Embedded-C++ as its *lingua franca*, but the user space client API is wrapped into C APIs.

----

Two C snippets showing how to retrieve the MAC address via IOKit.

    

/*

    taken from: http://developer.apple.com/mac/library/samplecode/GetPrimaryMACAddress/Introduction/Intro.html

    compile with:

    gcc -Wall -O3 -x objective-c -fobjc-exceptions -framework Foundation -framework CoreFoundation \
        -framework IOKit -o GetPrimaryMACAddress GetPrimaryMACAddress.c

   ./GetPrimaryMACAddress


    File:           GetPrimaryMACAddress.c
    
    Description:    This sample application demonstrates how to do retrieve the Ethernet MAC
                    address of the built-in Ethernet interface from the I/O Registry on Mac OS X.
                    Techniques shown include finding the primary (built-in) Ethernet interface,
                    finding the parent Ethernet controller, and retrieving properties from the
                    controller's I/O Registry entry.
                
    Copyright:      Copyright 2001-2005 Apple Computer, Inc. All rights reserved.
    
    Disclaimer:     IMPORTANT:  This Apple software is supplied to you by Apple Computer, Inc.
                    ("Apple") in consideration of your agreement to the following terms, and your
                    use, installation, modification or redistribution of this Apple software
                    constitutes acceptance of these terms.  If you do not agree with these terms,
                    please do not use, install, modify or redistribute this Apple software.

                    In consideration of your agreement to abide by the following terms, and subject
                    to these terms, Apple grants you a personal, non-exclusive license, under Apple���s
                    copyrights in this original Apple software (the "Apple Software"), to use,
                    reproduce, modify and redistribute the Apple Software, with or without
                    modifications, in source and/or binary forms; provided that if you redistribute
                    the Apple Software in its entirety and without modifications, you must retain
                    this notice and the following text and disclaimers in all such redistributions of
                    the Apple Software.  Neither the name, trademarks, service marks or logos of
                    Apple Computer, Inc. may be used to endorse or promote products derived from the
                    Apple Software without specific prior written permission from Apple.  Except as
                    expressly stated in this notice, no other rights or licenses, express or implied,
                    are granted by Apple herein, including but not limited to any patent rights that
                    may be infringed by your derivative works or by other works in which the Apple
                    Software may be incorporated.

                    The Apple Software is provided by Apple on an "AS IS" basis.  APPLE MAKES NO
                    WARRANTIES, EXPRESS OR IMPLIED, INCLUDING WITHOUT LIMITATION THE IMPLIED
                    WARRANTIES OF NON-INFRINGEMENT, MERCHANTABILITY AND FITNESS FOR A PARTICULAR
                    PURPOSE, REGARDING THE APPLE SOFTWARE OR ITS USE AND OPERATION ALONE OR IN
                    COMBINATION WITH YOUR PRODUCTS.

                    IN NO EVENT SHALL APPLE BE LIABLE FOR ANY SPECIAL, INDIRECT, INCIDENTAL OR
                    CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE
                    GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
                    ARISING IN ANY WAY OUT OF THE USE, REPRODUCTION, MODIFICATION AND/OR DISTRIBUTION
                    OF THE APPLE SOFTWARE, HOWEVER CAUSED AND WHETHER UNDER THEORY OF CONTRACT, TORT
                    (INCLUDING NEGLIGENCE), STRICT LIABILITY OR OTHERWISE, EVEN IF APPLE HAS BEEN
                    ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
                
    Change History (most recent first):
        
            <3>     09/15/05    Updated to produce a universal binary. Use kIOMasterPortDefault
                                instead of older IOMasterPort function. Print the MAC address
                                to stdout in response to <rdar://problem/4021220>.
            <2>     04/30/02    Fix bug in creating the matching dictionary that caused the
                                kIOPrimaryInterface property to be ignored. Clean up comments and add
                                additional comments about how IOServiceGetMatchingServices operates.
            <1>     06/07/01    New sample.
        
*/

#include <stdio.h>

#include <CoreFoundation/CoreFoundation.h>

#include <IOKit/IOKitLib.h>
#include <IOKit/network/IOEthernetInterface.h>
#include <IOKit/network/IONetworkInterface.h>
#include <IOKit/network/IOEthernetController.h>

static kern_return_t FindEthernetInterfaces(io_iterator_t *matchingServices);
static kern_return_t GetMACAddress(io_iterator_t intfIterator, UInt8 *MACAddress, UInt8 bufferSize);

// Returns an iterator containing the primary (built-in) Ethernet interface. The caller is responsible for
// releasing the iterator after the caller is done with it.
static kern_return_t FindEthernetInterfaces(io_iterator_t *matchingServices)
{
    kern_return_t       kernResult; 
    CFMutableDictionaryRef  matchingDict;
    CFMutableDictionaryRef  propertyMatchDict;
    
    // Ethernet interfaces are instances of class kIOEthernetInterfaceClass. 
    // IOServiceMatching is a convenience function to create a dictionary with the key kIOProviderClassKey and 
    // the specified value.
    matchingDict = IOServiceMatching(kIOEthernetInterfaceClass);

    // Note that another option here would be:
    // matchingDict = IOBSDMatching("en0");
        
    if (NULL == matchingDict) {
        printf("IOServiceMatching returned a NULL dictionary.\n");
    }
    else {
        // Each IONetworkInterface object has a Boolean property with the key kIOPrimaryInterface. Only the
        // primary (built-in) interface has this property set to TRUE.
        
        // IOServiceGetMatchingServices uses the default matching criteria defined by IOService. This considers
        // only the following properties plus any family-specific matching in this order of precedence 
        // (see IOService::passiveMatch):
        //
        // kIOProviderClassKey (IOServiceMatching)
        // kIONameMatchKey (IOServiceNameMatching)
        // kIOPropertyMatchKey
        // kIOPathMatchKey
        // kIOMatchedServiceCountKey
        // family-specific matching
        // kIOBSDNameKey (IOBSDNameMatching)
        // kIOLocationMatchKey
        
        // The IONetworkingFamily does not define any family-specific matching. This means that in            
        // order to have IOServiceGetMatchingServices consider the kIOPrimaryInterface property, we must
        // add that property to a separate dictionary and then add that to our matching dictionary
        // specifying kIOPropertyMatchKey.
            
        propertyMatchDict = CFDictionaryCreateMutable(kCFAllocatorDefault, 0,
                                                      &kCFTypeDictionaryKeyCallBacks,
                                                      &kCFTypeDictionaryValueCallBacks);
    
        if (NULL == propertyMatchDict) {
            printf("CFDictionaryCreateMutable returned a NULL dictionary.\n");
        }
        else {
            // Set the value in the dictionary of the property with the given key, or add the key 
            // to the dictionary if it doesn't exist. This call retains the value object passed in.
            CFDictionarySetValue(propertyMatchDict, CFSTR(kIOPrimaryInterface), kCFBooleanTrue); 
            
            // Now add the dictionary containing the matching value for kIOPrimaryInterface to our main
            // matching dictionary. This call will retain propertyMatchDict, so we can release our reference 
            // on propertyMatchDict after adding it to matchingDict.
            CFDictionarySetValue(matchingDict, CFSTR(kIOPropertyMatchKey), propertyMatchDict);
            CFRelease(propertyMatchDict);
        }
    }
    
    // IOServiceGetMatchingServices retains the returned iterator, so release the iterator when we're done with it.
    // IOServiceGetMatchingServices also consumes a reference on the matching dictionary so we don't need to release
    // the dictionary explicitly.
    kernResult = IOServiceGetMatchingServices(kIOMasterPortDefault, matchingDict, matchingServices);    
    if (KERN_SUCCESS != kernResult) {
        printf("IOServiceGetMatchingServices returned 0x%08x\n", kernResult);
    }
        
    return kernResult;
}
    
// Given an iterator across a set of Ethernet interfaces, return the MAC address of the last one.
// If no interfaces are found the MAC address is set to an empty string.
// In this sample the iterator should contain just the primary interface.
static kern_return_t GetMACAddress(io_iterator_t intfIterator, UInt8 *MACAddress, UInt8 bufferSize)
{
    io_object_t     intfService;
    io_object_t     controllerService;
    kern_return_t   kernResult = KERN_FAILURE;
    
    // Make sure the caller provided enough buffer space. Protect against buffer overflow problems.
    if (bufferSize < kIOEthernetAddressSize) {
        return kernResult;
    }
    
    // Initialize the returned address
    bzero(MACAddress, bufferSize);
    
    // IOIteratorNext retains the returned object, so release it when we're done with it.
    while ( (intfService = IOIteratorNext(intfIterator)) )
    {
        CFTypeRef   MACAddressAsCFData;        

        // IONetworkControllers can't be found directly by the IOServiceGetMatchingServices call, 
        // since they are hardware nubs and do not participate in driver matching. In other words,
        // registerService() is never called on them. So we've found the IONetworkInterface and will 
        // get its parent controller by asking for it specifically.
        
        // IORegistryEntryGetParentEntry retains the returned object, so release it when we're done with it.
        kernResult = IORegistryEntryGetParentEntry(intfService,
                                                   kIOServicePlane,
                                                   &controllerService);
        
        if (KERN_SUCCESS != kernResult) {
            printf("IORegistryEntryGetParentEntry returned 0x%08x\n", kernResult);
        }
        else {
            // Retrieve the MAC address property from the I/O Registry in the form of a CFData
            MACAddressAsCFData = IORegistryEntryCreateCFProperty(controllerService,
                                                                 CFSTR(kIOMACAddress),
                                                                 kCFAllocatorDefault,
                                                                 0);
            if (MACAddressAsCFData) {
                CFShow(MACAddressAsCFData); // for display purposes only; output goes to stderr
                
                // Get the raw bytes of the MAC address from the CFData
                CFDataGetBytes(MACAddressAsCFData, CFRangeMake(0, kIOEthernetAddressSize), MACAddress);
                CFRelease(MACAddressAsCFData);
            }
                
            // Done with the parent Ethernet controller object so we release it.
            (void) IOObjectRelease(controllerService);
        }
        
        // Done with the Ethernet interface object so we release it.
        (void) IOObjectRelease(intfService);
    }
        
    return kernResult;
}

int main(int argc, char *argv[])
{
    kern_return_t   kernResult = KERN_SUCCESS; // on PowerPC this is an int (4 bytes)
/*
 *  error number layout as follows (see mach/error.h and IOKit/IOReturn.h):
 *
 *  hi                     lo
 *  | system(6) | subsystem(12) | code(14) |
 */

    io_iterator_t   intfIterator;
    UInt8           MACAddress[kIOEthernetAddressSize];
 
    kernResult = FindEthernetInterfaces(&intfIterator);
    
    if (KERN_SUCCESS != kernResult) {
        printf("FindEthernetInterfaces returned 0x%08x\n", kernResult);
    }
    else {
        kernResult = GetMACAddress(intfIterator, MACAddress, sizeof(MACAddress));
        
        if (KERN_SUCCESS != kernResult) {
            printf("GetMACAddress returned 0x%08x\n", kernResult);
        }
        else {
            printf("This system's built-in MAC address is %02x:%02x:%02x:%02x:%02x:%02x.\n",
                    MACAddress[0], MACAddress[1], MACAddress[2], MACAddress[3], MACAddress[4], MACAddress[5]);
        }
    }
    
    (void) IOObjectRelease(intfIterator);   // Release the iterator.
        
    return kernResult;
}



----

    

/*

getmacaddress - get MAC address

C snippet posted by Guiyon on Apr 23, 2009, 10:03 AM at:

"how to find mac address using xcode",
http://forums.macrumors.com/showthread.php?t=689645

compile with:

gcc -Wall -O3 -x objective-c -fobjc-exceptions -framework Foundation -framework CoreFoundation -framework IOKit -o getmacaddress getmacaddress.c

./getmacaddress


"One important thing to note is that the CF* and NS* classes are toll-free bridged. That is, 
if you have some CFStringRef, for example, you can just typecast it to NSString* and use it 
as you would an NSString. This also means the same retain/release requirements apply."

*/

#import <Foundation/Foundation.h>

#include <IOKit/IOKitLib.h>
#include <IOKit/network/IOEthernetInterface.h>
#include <IOKit/network/IOEthernetController.h>

static NSDictionary* GetMACAddresses( io_iterator_t netIterator );
static kern_return_t FindNetworkInterfaces( io_iterator_t *services );
static NSString* bytesToHexString( NSData* data );

int main (int argc, const char * argv[]) {
  io_iterator_t intfIterator;
  kern_return_t kernResult = KERN_FAILURE;
  
  NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

  // NSLog(@"Detecting Network Interfaces...");
  
  kernResult = FindNetworkInterfaces( &intfIterator );
  if( KERN_SUCCESS != kernResult ) {
    printf( "findNetworkInterfaces failed\n" );

  } else {

    // NSLog( @"Found one of more valid interfaces. Extracting MAC addresses..." );

    NSDictionary* intfDictionary = GetMACAddresses( intfIterator );

    /*
    for( id key in intfDictionary ) {
      NSLog( @"  %@  =>  %@", key, [intfDictionary objectForKey: key] );
    }
   */

   /* ADDED !!! */
   /* more portable code than: for( id key in intfDictionary ) { ... */
   NSArray* keys = intfDictionary allKeys] sortedArrayUsingSelector:@selector(caseInsensitiveCompare:)];
   int num = [keys count];
   int i;
   for (i = 0; i < num; i++) 
   {
      /* [[NSLog( @"  %@  =>  %@", [keys objectAtIndex: i], [intfDictionary objectForKey: [keys objectAtIndex: i]] ); */
      printf("%s => %s\n", keys objectAtIndex: i] UTF8String], [[intfDictionary objectForKey: [keys objectAtIndex: i UTF8String]);
   }

    IOObjectRelease( intfIterator );
  }
  
  /* [pool drain]; */
  [pool release];

  return 0;
}
/* Search through each of the Network interfaces identified
 *  in the netIterator and pull out the MAC address and the BSD
 *  name of the interface. Then convert the raw MAC address into
 *  a readable string and return an NSDictionary with a mapping of
 *  BSD Name (NSString*) => MAC Address (NSString*) for each interface
 */
static NSDictionary* GetMACAddresses( io_iterator_t netIterator ) {
  NSMutableDictionary*    etherDictionary = nil;
  kern_return_t           kernResult = KERN_FAILURE;
  io_object_t             interfaceService;
  io_object_t             controllerService;
  
  while( ( interfaceService = IOIteratorNext(netIterator) ) ) {
    CFTypeRef MACAddrAsCFData = NULL;
    CFTypeRef BSDNameAsCFString = NULL;
    
    kernResult = IORegistryEntryGetParentEntry( interfaceService,
                                                kIOServicePlane,
                                                &controllerService );
    if( KERN_SUCCESS != kernResult ) {
      printf( "IORegistryEntryGetParentEntry failed with 0x%08x\n", kernResult );
    } else {
      MACAddrAsCFData = IORegistryEntryCreateCFProperty( controllerService,
                                                         CFSTR(kIOMACAddress),
                                                         kCFAllocatorDefault,
                                                         0 );
      
      BSDNameAsCFString = IORegistryEntryCreateCFProperty( interfaceService,
                                                           CFSTR("BSD Name"),
                                                           kCFAllocatorDefault,
                                                           0 );
      
      if( MACAddrAsCFData && BSDNameAsCFString ) {
        if( nil == etherDictionary ) {
          etherDictionary = [[NSMutableDictionary alloc] init]; 
        }
        
        [etherDictionary setObject:bytesToHexString((NSData*)MACAddrAsCFData)
                            forKey:(NSString*)BSDNameAsCFString];
        
      }

      if( nil != BSDNameAsCFString ) {
            CFRelease( BSDNameAsCFString );
      }

      if( nil != MACAddrAsCFData ) {
            CFRelease( MACAddrAsCFData );
      }
    }
  }
  
  return (nil == etherDictionary) ? nil : (NSDictionary*)[etherDictionary autorelease];
}
/* Search through the IOServicePlane and find any services that
 *  represent an IOEthernetInterface. If you wanted to find a
 *  MAC address for a specific interface you could replace the
 *  IOServiceMatching calls with IOBSDNameMatching
 */
static kern_return_t FindNetworkInterfaces( io_iterator_t *services ) {
  CFMutableDictionaryRef  matchClasses = NULL;
  kern_return_t           kernResult = KERN_FAILURE;
  mach_port_t             machPort;
  
  kernResult = IOMasterPort( MACH_PORT_NULL, &machPort );
  if( KERN_SUCCESS != kernResult ) {
    printf( "IOMasterPort failed: %d\n", kernResult );
  }
  
  matchClasses = IOServiceMatching( kIOEthernetInterfaceClass );
  if( NULL == matchClasses ) {
    printf( "IOServiceMatching returned a NULL dictionary" );
  }
  
  kernResult = IOServiceGetMatchingServices( machPort, matchClasses, services );
  if( KERN_SUCCESS != kernResult ) {
    printf( "IOServiceGetMatchingServices failed: %d\n", kernResult );
  }
  
  return kernResult;
}
static NSString* bytesToHexString( NSData* data ) {
  NSMutableString*  result = [[NSMutableString alloc] initWithCapacity: [data length] << 1];
  UInt8*  mbytes = (UInt8*)[data bytes];
  char    hBytes[8] = {'\0'};
  int     i = 0;
  
  for( ; i < [data length]; i++ ) {
    snprintf( hBytes, 3, "%02X", mbytes[ i ] );
    if( 0 == i ) {
      [result appendFormat:@"%s", hBytes];
    } else {
      [result appendFormat:@":%s", hBytes];
    }
  }
  
  return (NSString*)[result autorelease];
}



----

A command line tool to list mounted USB devices and their respective volume paths; uses IOKit and man 2 getfsstat to get the job done.

The man 2 getfsstat code could be replaced by Apple's IOKit-based sample code VolumeToBSDNode though.

    

/*

usbdevs - list mounted USB devices & their volume paths on Mac OS X

References:

- http://stackoverflow.com/questions/2459414/osx-how-to-get-a-volume-name-or-bsd-name-from-a-iousbdeviceinterface-or-locati
- http://superuser.com/questions/103755/whats-up-with-stat-on-mac-os-x-darwin-or-filesystems-without-names
- http://stackoverflow.com/questions/1698124/how-to-tell-if-a-given-path-is-mounted-removable-media-in-mac-os-x
- http://developer.apple.com/mac/library/samplecode/VolumeToBSDNode/Introduction/Intro.html

compile with:

gcc -Wall -O3 -x objective-c -fobjc-exceptions -framework Foundation -framework CoreFoundation \
    -framework IOKit -o usbdevs usbdevs.c

usage:

./usbdevs
./usbdevs -z | xargs -0 printf "%s\n"

*/

#import <stdio.h>
#import <stdlib.h>
#import <string.h>
#import <sys/param.h>
#import <sys/ucred.h>
#import <sys/mount.h>

#import <Foundation/Foundation.h>
#import <CoreFoundation/CoreFoundation.h>
#import <IOKit/IOKitLib.h>
#import <IOKit/IOMessage.h>
#import <IOKit/IOCFPlugIn.h>
#import <IOKit/usb/IOUSBLib.h>
#import <IOKit/IOBSD.h>



int main(int argc, char *argv[]) {

   NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

   mach_port_t             master_port;
   kern_return_t           k_result = KERN_FAILURE;
   io_iterator_t           iterator = 0;
   io_service_t            usb_device_ref;
   CFMutableDictionaryRef  matching_dictionary = NULL; 

   char devicePath[MAXPATHLEN*2] = {0};

   int print0 = 0;
   size_t pathlen = 0;
   const char *deviceName = 0;
   CFStringRef bsdName = NULL;

   if (argc > 2) return 1;

   if (argc == 2)
   {
      if ( (strcmp(argv[1], "-z") == 0) )
      {
         print0 = 1;
      }
      else if ( (strcmp(argv[1], "-h") == 0) )
      {
         fprintf(stdout, "usage:  %s [-hz]\n\t-z: null-terminate output (ASCII nul character)\n", argv[0]); 
         return 0;
      }else{
         fprintf(stderr, "usage: %s [-hz]\n", argv[0]); 
         return 1;
      }
   }


   // man 2 getfsstat
   // getfsstat -- get list of all mounted file systems

   struct statfs *buf;
   int flags = MNT_NOWAIT, num_fs, num_stat, i;
   unsigned bufsz;

   num_fs = getfsstat(NULL, 0, flags);
   if (num_fs < 0) {
      perror("unable to count mounted filesystems: getfsstat");
      exit(1);
   }

   bufsz = sizeof(*buf) * num_fs;
   buf = malloc(bufsz);
   if (!buf) {
      perror("unable to allocate %u statfs structs");
      exit(1);
   }

   //fprintf(stderr, "p=%p\n", buf);

   num_stat = getfsstat(buf, bufsz, flags);
   if (num_stat < 0) {
       perror("unable to getfsstat");
       exit(1);
   }

   if (num_stat != num_fs) {
      fprintf(stderr, "Hmm, expected %u, got %d.\n", num_fs, num_stat);
   }


   // first create a master_port
   k_result = IOMasterPort(MACH_PORT_NULL, &master_port);
   if (KERN_SUCCESS != k_result) 
   {
      fprintf(stderr, "could not create master port, err = %d\n", k_result);
   }

   if ((matching_dictionary = IOServiceMatching(kIOUSBDeviceClassName)) == NULL)
   {
      fprintf(stderr, "could not create matching dictionary, err = %d\n", k_result);
   }

   k_result = IOServiceGetMatchingServices(master_port, matching_dictionary, &iterator);
   if (KERN_SUCCESS != k_result) 
   {
      fprintf(stderr, "could not find any matching services, err = %d\n", k_result);
   }

   while ((usb_device_ref = IOIteratorNext(iterator)))
   {
   
      bsdName = (CFStringRef) IORegistryEntrySearchCFProperty (usb_device_ref,
                                                               kIOServicePlane,
                                                               CFSTR ( kIOBSDNameKey ),
                                                               kCFAllocatorDefault,
                                                               kIORegistryIterateRecursively );
      if (!bsdName) continue;

      deviceName = [[NSString stringWithFormat: @"%@", bsdName] UTF8String];

      //printf("%s\n", deviceName);

      devicePath[0] = '\0';
      strcat(devicePath, "/dev/");
      strcat(devicePath, deviceName);

      pathlen = strlen(devicePath);

      //printf("pathlen: %zu\n", pathlen);
      //printf("devicePath: %s\n", devicePath);

      for (i = 0; i < num_stat; i++) {

         if (strncmp(buf[i].f_mntfromname, devicePath, pathlen) == 0)
         {
            if ( print0 == 0 )
               fprintf(stdout, "%s %s\n", buf[i].f_mntfromname, buf[i].f_mntonname);
            else
               fprintf(stdout, "%s %s%c", buf[i].f_mntfromname, buf[i].f_mntonname, 0);  
               // null-terminate output (ASCII nul character)
         }

      }

      CFRelease(bsdName);

      IOObjectRelease(usb_device_ref);            // no longer need this reference


   } // while


   [pool release];

   return 0;

}


