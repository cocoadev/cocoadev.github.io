---
layout: page
title: DiscoveringSystemServices
---



I'd like to write a command-line tool to invoke Cocoa system services -- the things usually found in the Services Menu.  It seems easy to invoke them using the function
    
BOOL NSPerformService(NSString *serviceItem, NSPasteboard *pboard)

in the application kit.  My problem is in determining which services are available.  I've scoured the Apple docs but can't find any way to extract the list of service names.  Does anyone know how to do this?  Any help appreciated.

--

Use 	(NSArray *)CFServiceControllerCopyServicesEntries().

Example code (from DavidMcCabe, not the original questioner). It would be great if somebody could explain the questions in the comments.

    
- (NSEnumerator *)serviceEnumerator {
	NSArray *services = (NSArray *)CFServiceControllerCopyServicesEntries();
	NSEnumerator *se = [services objectEnumerator];
	NSDictionary *serviceEntry;
	NSMutableArray *result = [NSMutableArray arrayWithCapacity:[services count]];

	while( serviceEntry = [se nextObject] ) {
		id m;
		m = serviceEntry valueForKey:@"[[NSMenuItem"] valueForKey:@"default"];
		if( m == nil ) {
			// There are a couple of entries with no labels; don't know what they are.
			//[result addObject: [serviceEntry valueForKey:@"NSMessage"]];
		} else { // would like to see if they're enabled here. we can get NSSendTypes.
			[result addObject: m];
		}
	}
	return [result objectEnumerator];
}


The first comment indicates that the specified service has no menu item label, and the commented out code passes the message (@"NSMessage" key refers to a string?)  instead of the menu label. The 'else' comment says it would be nice to know if the service is enabled, and we might want to get the object refered to by the @"NSSendTypes" key, which is an array holding type names which the service accepts. Now that I think about it, maybe the service system could be wrapped in a class which makes it easier to access and understand this stuff. I just this very minute looked at apple docs "System Services" to find this out, the documentation is a little obtuse, but seems comprehensive... JeremyJurksztowicz

----

Hi, I'm back. I just whipped this up on a windows notepad clone at work. I looked at the online documentation, but since It's quitting time in a few minutes I don't feel like combing through the code now. I have become so dependant on syntax coloring. How did people do it in black and white? There is certainly a better way to do this, but I had fun at work today, so, who cares? -JeremyJurksztowicz

    
// Copyright (C) Jeremy Jurksztowicz, 2008
// Free to do anything you like with this code, including take credit for it,
// but that would be really petty and immature. You're a nice person, right?
///////////////////////////////////////////////////////////////////////////////
///////////////////////////////////////////////////////////////////////////////
enum ServiceInputMechanism
{
	ServiceInput_Standard,
	ServiceInput_UnixStdio,
	ServiceInput_MapFile
};
///////////////////////////////////////////////////////////////////////////////
///////////////////////////////////////////////////////////////////////////////
NSArray * SystemServices ( );
///////////////////////////////////////////////////////////////////////////////
///////////////////////////////////////////////////////////////////////////////
@interface SystemService
{
	NSDictionary * properties;
}
///////////////////////////////////////////////////////////////////////////////
///////////////////////////////////////////////////////////////////////////////
- (NSString*) 	messageString;
- (NSString*)	filterMessageString;
- (NSString*)	portName;
- (NSString*)	menuItemString;
- (NSString*)	keyEquivalent;
- (NSArray*)	sendTypes;
- (NSArray*)	returnTypes;
- (NSString*)	userDataString;
- (double)	timeout; // In milliseconds

- (BOOL) isIdentityFilter;
- (ServiceInputMechanism) inputMechanism;

- (BOOL) performServiceOnData:(NSData*)data ofType:(NSString*)type outData:(NSData**)output;

@end
///////////////////////////////////////////////////////////////////////////////
///////////////////////////////////////////////////////////////////////////////
NSArray * SystemServices ( )
{
	NSArray * services = (NSArray *)CFServiceControllerCopyServicesEntries();
	if(services)
	{
		NSMutableArray * result = [NSMutableArray arrayWithCapacity:[services count]];
	
		NSEnumerator * serviceEnum = [services objectEnumerator];
		NSDictionary * serviceInfo;
		while(serviceInfo = [serviceEnum nextObject])
		{
			SystemService * service = [[[SystemService alloc] init] autorelease];
			service->properties = [serviceInfo retain];
		
			[result addObject:service];
		}
		[services release];
		
		return result;
	}
	return nil;
}
///////////////////////////////////////////////////////////////////////////////
///////////////////////////////////////////////////////////////////////////////
@implementation ServiceInfo

- (void) dealloc
{
	[properties release];
	[super dealloc];
}

- (NSString*) 	messageString		{ return [properties objectForKey:@"NSMessage"]; }
- (NSString*) 	filterMessageString	{ return [properties objectForKey:@"NSFilter"]; }
- (NSString*)	portName		{ return [properties objectForKey:@"NSPortName"]; }
- (NSString*)	menuItemString		{ return properties objectForKey:@"[[NSMenuItem"] objectForKey:@"default"]; }
- (NSString*)	keyEquivalent		{ return properties objectForKey:@"[[NSKeyEquivalent"] objectForKey:@"default"]; }
- (NSArray*)	sendTypes		{ return [properties objectForKey:@"NSSendTypes"]; }
- (NSArray*)	returnTypes		{ return [properties objectForKey:@"NSReturnTypes"]; }
- (NSString*)	userDataString		{ return [properties objectForKey:@"NSUserData"]; }
- (NSString*)	timeout			{ return properties objectForKey:@"[[NSTimeout"] doubleValue]; }

- (BOOL) isIdentityFilter
{
	return properties objectForKey:@"[[NSInputMechanism"] isEqualToString:@"NSIdentity"];
}

- (ServiceInputMechanism) inputMechanism
{
	NSString * inputMechanismString = [properties objectForKey:@"NSInputMechanism"];
	if([inputMechanismString isEqualToString:@"NSUnixStdio"])
		return ServiceInput_UnixStdio;
	else if([inputMechanismString isEqualToString:@"NSMapFile"])
		return ServiceInput_MapFile;
	else
		return ServiceInput_Standard;
}

- (BOOL) performServiceOnData:(NSData*)data ofType:(NSString*)type outData:(NSData**)output outType:(NSString*)outputType
{
	if(!self sendTypes] containsObject:type] || ![[self returnTypes] containsObject:outputType])
		return NO;

	[[NSPasteboard * pboard = [NSPasteboard pasteboardWithName:@"MyServicePasteboard"];
	
	[pboard declareTypes:[NSArray arrayWithObject:type] owner:nil];
	[pboard setData:data forType:type];

	BOOL result = NSPerformService([self menuItemString], pboard);
	if(result)
	{
		if([pboard availableTypeFromArray:[NSArray arrayWithObject:outputType]])
		{
			*output = [pboard dataForType:outputType];
		}
		else return NO;
	}
	return result;
}

@end

