---
layout: page
title: CFStreamErrorCodes
---

A CFStreamError is defined as the following :
    
typedef struct {
	CFStreamErrorDomain domain;
	SInt32 error
} CFStreamError;

But when you get such an error, you only get a domain code and an error code, which doesn't really mean anything. To make it human readable, you need to compare the domain to all the existing domain constants :
    
CFStreamError error;
if (error.error != 0) {
	if (error.domain == kCFStreamErrorDomainPOSIX) {
		NSLog(@"POSIX error domain, CFSTream.h says error code is to be interpreted using sys/errno.h.");
	} else if (error.domain == kCFStreamErrorDomainMacOSStatus) {
		OSStatus macError = (OSStatus)error.error;
		NSLog(@"OS error: %d, CFStream.h says error code is to be interpreted using MacTypes.h.", macError);
	} else if (error.domain == kCFStreamErrorDomainHTTP) {
		NSLog(@"HTTP error domain, CFHTTPSteam.h says error code is the HTTP error code.");
	} else if (error.domain == kCFStreamErrorDomainMach) {
		NSLog(@"Mach error domain, CFNetServices.h says error code is to be interpreted using mach/error.h.");
	} else if (error.domain == kCFStreamErrorDomainNetDB) {
		NSLog(@"NetDB error domain, CFHTTPHost.h says error code is to be finterpreted using netdb.h.");
	} else if (error.domain == kCFStreamErrorDomainCustom) {
		NSLog(@"Custom error domain");
	} else if (error.domain == kCFStreamErrorDomainSystemConfiguration) {
		NSLog(@"System Configuration error domain, CFHost.h says error code is to be interpreted using SystemConfiguration/SystemConfiguration.h.");
	}
	NSLog(@"error %d domain %d", error.error, error.domain);
}

Once you have the domain, look at the corresponding header file to get the error description.

(Thanks http://www.cocoabuilder.com/archive/message/cocoa/2004/9/15/117526 and http://www.sporkstorms.org/code/DAClient/DAClient.m)

