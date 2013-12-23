---
layout: page
title: ParsingHTTPHeaders
---

I'm writing an network application that will need to parse HTTP headers. 
Does anyone know of a library or framework (Objective-C or C) that can do this?

----

The CFHTTPMessage 'class' (it's C-based but somewhat object-oriented nonetheless) that's part of Apple's CFNetwork framework can parse HTTP.  CFNetwork is a sub-framework of the CoreServices framework.  -- Bo

----

Use NSHTTPURLResponse class in Web Foundation. 

----

Note that although the docs say you can use [NSHTTPURLResponse allHeaderFields] to "see the 'raw' header information returned by the HTTP server", it isn't really "raw".  All the "Set-Cookie" headers, for example, are collapsed down to just one with the cookie info concatenated together *separated by commas*!  The insanity of this will become clear when you try to unseparate the cookies if you need to deal with them individually as you'll note that the "expires" clause also contains commas making the parsing infinitely harder than it would be if they'd chosen a sensible separator.  In theory you should be able to use [NSHTTPCookie cookiesWithResponseHeaderFields] to undo the concatenation but it's buggy and can't handle cookies that lack explicit "domain" clauses. -- Perry

----

More precisely, it appears that -[NSHTTPCookie initWithProperties] will not bake you a cookie if the ingredients don't include a "Domain" property.  This is probably the underlying reason why +cookiesWithResponseHeaderFields:forURL: omits such cookies; it probably sends -initWithProperties to make the cookies after it is done parsing.

Advice: Don't spend hours making a nice parser to replace +cookiesWithResponseHeaderFields:forURL.  :(

Jerry Krinock

----

I think that missing domain part is not a problem when parsing.
Below is example where there are two cookies (comma separated) and one of them have no domain. This code list both cookies. Problem is propably that it should return only "name2" cookie because "name1" cookie have different domain (.test.pl) than asked one (www.foo.pl).

Note it doesn't do any security checks (even described in RFC)

    
	int i;
	NSDictionary *dic = [NSDictionary dictionaryWithObject:@"name=value; path=/; domain=.test.com;, name2=value2; path=/;" forKey:@"Set-Cookie"];
	NSArray *cookies = [NSHTTPCookie cookiesWithResponseHeaderFields:dic forURL:[NSURL URLWithString:@"http://www.foo.com/"]];
	
	for (i = 0; i < [cookies count]; i++)
	{
		NSHTTPCookie *cookie = [cookies objectAtIndex:i];
		NSLog(@"%@ for %@",[cookie name], [cookie domain]);
	}
 

----

After failing with the above code I decided to do it manually on the fly. I'm only looking for specific cookies if they are present. So the following code works fine if, for example you are looking for someCookieName01 and someCookieName02:

    
	NSHTTPURLResponse *headerResponse;
	headerResponse = (NSHTTPURLResponse *)response;
	
	NSDictionary *headerDictionary;
	headerDictionary = headerResponse allHeaderFields] retain];
	
        //checking response data
	[[NSLog(@"NSHTTPURLResponse: %@",headerResponse);
	NSLog(@"NSDictionary: %@",headerDictionary);
	NSLog(@"Header Set-Cookie: %@",[headerDictionary valueForKey:@"Set-Cookie"]);
	
	
	NSString *theRAWCookie;
	theRAWCookie = (NSString *)[headerDictionary valueForKey:@"Set-Cookie"];
	
	NSArray *headerArray = [theRAWCookie componentsSeparatedByCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@";, "]];
	
	for (NSString *cookie in headerArray) 
	{
		//NSLog(@"Cookie: %@", cookie);
		
		if ([cookie hasPrefix:@"someCookieName01="])
		{
			NSLog(@"Cookie: %@", cookie); 
			self.UserLoginCookie = cookie;
		} else if ([cookie hasPrefix:@"someCookieName02="])
		{
			NSLog(@"Cookie: %@", cookie);
			self.SessionIDCookie = cookie;
		}
		
	}
		theRAWCookie = nil;
		[headerDictionary release];
		[headerResponse release];

