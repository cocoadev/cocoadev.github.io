---
layout: page
title: ExtraDataInSomeRendezvousServices
---

iTunes includes lots of extra info in its rendezvous service broadcast, such as:

txtver

Version

iTSh Version

Machine ID

Machine Name

Password

etc

I don't see any way to publish extra flags like this in the official API.  Anyone know how this is done and how it is parsed by the NSNetserviceBrowser?

EcumeDesJours

----

That's the     -protocolSpecificInformation and     -setProtocolSpecificInformation: methods.  The format used for the strings is "x=y\001u=v\001w=z" (that's ASCII char 0x01 separating each line), so, for example, the code to encode and decode a dictionary of strings (I'm gonna skip the class/error checking) would be:
    
NSString* encodeOpenTalkTXTRecord(NSDictionary* recs)
{
	NSMutableArray* encodedRecs = [NSMutableArray arrayWithCapacity:[recs count]];
	NSEnumerator* dictKeyEnum = [recs keyEnumerator];
	NSString* currKey;

	while (currKey = [dictKeyEnum nextObject]) {
		NSString* currValue = [recs objectForKey:currKey];
		[encodedRecs addObject:[NSString stringWithFormat:@"%@=%@", currKey, currValue]];
	}
	return [encodedRecs componentsJoinedByString:@"\001"];
}
NSDictionary* decodeOpenTalkTXTRecord(NSString* rec)
{
	NSArray* encodedRecs = [rec componentsSeparatedByString:@"\001"];
	NSMutableDictionary* decodedRecs = [NSMutableDictionary dictionaryWithCapacity:[encodedRecs count]];
	NSEnumerator* recEnum = [encodedRecs objectEnumerator];
	NSString* currRec;
	
	while (currRec = [recEnum nextObject]) {
		NSArray* currRecParts = [currRec componentsSeparatedByString:@"="];
		// keep in mind that the records are not required to follow this format
		// so you may wish to check whether you've actually got a two string array
		[decodedRecs setObject:[currRecParts objectAtIndex:1] forKey:[currRecParts objectAtIndex:0]];
	}
	return decodedRecs;
}


I just typed this up in the browser, so it may not compile, much less work.  If there's a problem, post here and I'll figure out what's the matter.  -- Bo

