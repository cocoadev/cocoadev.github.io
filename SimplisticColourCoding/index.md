---
layout: page
title: SimplisticColourCoding
---

Hey, all. Recently wrote a project that stores a few NSColor objects in the UserDefaults. I *still* haven't really figured out NSCoding and NSArchiver and all that, so I just wrote a quick hack of a function which takes a colour, converts it to RGB space if necessary, and returns an NSDictionary with keys Red, Green, Blue, and Alpha. Here goes:

    
NSDictionary *BCDictionaryFromColour(NSColor *colour)
{
	NSMutableDictionary *dict = [NSMutableDictionary dictionary];
	if([colour colorSpaceName] == NSCalibratedRGBColorSpace)
	{
		[dict setObject:[NSNumber numberWithFloat:[colour redComponent]]
		forKey:@"Red"];
		[dict setObject:[NSNumber numberWithFloat:[colour greenComponent]]
		forKey:@"Green"];
		[dict setObject:[NSNumber numberWithFloat:[colour blueComponent]]
		forKey:@"Blue"];
		[dict setObject:[NSNumber numberWithFloat:[colour alphaComponent]]
		forKey:@"Alpha"];
	}
	else
	{
		return BCDictionaryFromColour([colour
		colorUsingColorSpaceName:NSCalibratedRGBColorSpace]);
	}
	return dict;
}


And then of course there's the vastly simpler partner function which takes such a dictionary and makes a colour out of it:

    
NSColor *BCColourFromDictionary(NSDictionary *dict)
{
	return [NSColor colorWithCalibratedRed:dict objectForKey:@"Red"] floatValue]
		green:[[dict objectForKey:@"Green"] floatValue]
		blue:[[dict objectForKey:@"Blue"] floatValue]
		alpha:[[dict objectForKey:@"Alpha"] floatValue;
}

