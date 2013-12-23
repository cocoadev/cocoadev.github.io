---
layout: page
title: SendingAppleEventsToSystemProfiler
---

(Based on mailing list posts by Nathan Day and Cynthia Copenhagen)

**Q: **
How I can get strings out of some of the fields in the Apple System Profiler.

The Apple Script goes something like:

    
tell application "Apple System Profiler"
    get system version as text
end tell


What I am not sure of is how to go through a Cocoa script to call the AppleScript command.

----

**A: **
This is pretty much cut straight out out of a one of my own projects 
with some small changes, you have to include Carbon. I haven't found a 
way to do this sort of thing yet using straight Cocoa. 

    
+ (id)compileExecuteString:(NSString *) aString
{
	OSAID			theResultID;
	AEDesc			theResultDesc = { typeNull, NULL },
					theScriptDesc = { typeNull, NULL };
	id				theResultObject = nil;

	if( (AECreateDesc( typeChar, [aString cString], [aString 
cStringLength], &theScriptDesc) ==  noErr) && (OSACompileExecute
( [AppleScriptObject OSAComponent], &theScriptDesc, kOSANullScript, 
kOSAModeNull, &theResultID) ==  noErr ) )
	{
		if( OSACoerceToDesc( [AppleScriptObject OSAComponent], 
theResultID, typeWildCard, kOSAModeNull, &theResultDesc ) == noErr )
		{
			if( theResultDesc.descriptorType == typeChar )
			{
				theResultObject = [NSString stringWithAEDesc:&theResultDesc];
				AEDisposeDesc( &theResultDesc );
			}
		}
		AEDisposeDesc( &theScriptDesc );
		if( theResultID != kOSANullScript )
			OSADispose( [AppleScriptObject OSAComponent], theResultID );
	}
	
	return theResultObject;
}

+ (ComponentInstance)OSAComponent
{
	static ComponentInstance		theComponent = NULL;
	
	if( !theComponent )
	{
		theComponent = OpenDefaultComponent( kOSAComponentType, 
kAppleScriptSubtype );
	}
	return theComponent;
}

@implementation NSString (AEDescCreation)

/*
  * + stringWithAEDesc:
  */
+ (id)stringWithAEDesc:(const AEDesc *)aDesc
{
	NSData			* theTextData;
	
	theTextData = [NSData dataWithAEDesc: aDesc];
	
	return ( theTextData == nil ) ? nil : [[[NSString 
alloc]initWithData:theTextData encoding:NSMacOSRomanStringEncoding] 
autorelease];
}

@end



----
====
To use the AppleScript above use the following code (Cocoa, Mac OS X 10.2 and above):

    

- (void)getSystemVersion
{
 NSString *myCode;
 NSAppleScript *myAppleScript;

 myCode = @"tell application \"Apple System Profiler\"\n set sysVersion to get system version as text\n display dialog sysVersion\n end tell";
 myAppleScript alloc] initWithSource:[[[NSString stringWithString:myCode]];
 [myAppleScript executeAndReturnError:nil];
 [myAppleScript release];
}


- FernandoLucasSantos(Brazil)

