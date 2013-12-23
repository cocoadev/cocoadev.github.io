---
layout: page
title: MemLeaksWithMultiThreadNSURLConnection
---



Hi There:

I'm having some issues with Memory Leaking from NSURLConnection (either from calling it directly, or when it's used in another method [eg initWithContentsOfURL]. The problem seems to appear when it's used on a thread other than the main one. Here's my code:

    

// This is released when the program begins, by a thread that is in turn released by the initWithFrame method

- (void)getNextPhotos:(id)anObject
{	
	
	NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
	
	gettingMorePhotos = YES;
	
	// start a loop...
	while (1) {
	
	NSLog(@"checking to see if we need to get some photos....");
	
	if ([nextPhotos count] < 10 && hasSession == YES) {
		
		// take the friends and shuffle it
		if ([filteredFriends count] > 0) {
			[self randomizeArray:filteredFriends];
			
			//[nextPhotos removeAllObjects];
			
			// grab the top five, get their photos
			
				
				if ([filteredFriends objectAtIndex:0]) {
				
					int i;
					for (i=0; i < 3; i++) {
						// changed this loop around a bit to ensure that we get enough photos for when sequential = 1
						
						if ([filteredFriends objectAtIndex:i]) {
							
							[self getUserPhotos:filteredFriends objectAtIndex:i] uid;
							
							
							}
						
						if ([nextPhotos count] > 10) {
							i = 3;
							
							}
						
						}
					}
				}
		}
	
	// sleep for a bit
	sleep(3);
	
	}	
		[pool release];
	
}



/// the method above in turn calls this method...
- (void)getUserPhotos:(NSString *)uid
{
	
	
	
	// get the user photos and put them in an array
	tempphotos = [[NSMutableArray alloc] initWithCapacity:100];
	
	NSMutableDictionary *tempDictionary = [[NSMutableDictionary alloc] init];	
	[tempDictionary setValue:@"facebook.photos.get" forKey:@"method"];
	[tempDictionary setValue:api_key forKey:@"api_key"];
	[tempDictionary setValue:@"1.0" forKey:@"v"];
	[tempDictionary setValue:session forKey:@"session_key"];
	NSString * current_time = [self getCurrentTime];
	[tempDictionary setValue:current_time forKey:@"call_id"];
	[tempDictionary setValue:uid forKey:@"subj_id"];
	
	//NSLog(uid);
	
	NSString* s = [self createSecret:tempDictionary];		
	
	[tempDictionary release];
	
	NSMutableString *postArgs = [[NSMutableString alloc]  initWithString:@""];
	
	NSArray *urlArguments = [NSArray arrayWithObjects:@"method=facebook.photos.get&sig=", s, @"&api_key=", api_key, @"&v=", @"1.0", @"&session_key=", session, @"&call_id=", current_time, @"&subj_id=", uid, nil];
	
	[postArgs appendString:[urlArguments componentsJoinedByString:@""]];
	//NSLog(urlToCall);
	
	
	// [photos release];
	
	
	NSXMLDocument *tokenXML = [[NSXMLDocument alloc] initWithData:[self fetchXMLData:postArgs]
														 options:nil
														  error:nil];
	//NSString *tempString;
	
	
	if([tokenXML rootElement] name] isEqualToString:@"photos_get_response"])
	{
			[[NSLog(@"photos getting..");
			
			int i;
			int i2;
			int i4;
			id aNode;
			for (i = 0; i < tokenXML rootElement] childCount]; i++) {
				
				aNode = [[tokenXML rootElement] childAtIndex: i];
				
				
				// ok, now loop through nodes
				for (i2=0; i2 < [aNode childCount]; i2++) {
					
					if ([[[aNode childAtIndex:i2] name] isEqualTo:@"src_big"]) {
						/// add to photos
						
						// create new photo object
						Photo *tempPhoto = [[Photo alloc] init];
						
						// will this work??
						[[NSString* tempString = [[NSString alloc] initWithString:aNode childAtIndex:i2] stringValue;
						[tempPhoto setUrl:tempString];
						[tempString release];
						
						/// find the friend's name
						for (i4=0; i4 < [friends count]; i4++) {
							
							if ([friends objectAtIndex:i4] uid] isEqualToString:uid]) {
								// found friend
								
								 [tempPhoto setName:[[friends objectAtIndex:i4] name;
								
								}
							
							}
						
						[tempphotos addObject:tempPhoto];
						
						[tempPhoto release];
						
						}
					
					}
				
				}
			
			if ([tempphotos count] > 1) {
				[self randomizeArray:tempphotos];
			}
			
			
			// limit to sequential photos
			int i3;
			for (i3 = 0; i3 < sequentialPhotos; i3++) {
				
				if (i3 < [tempphotos count]) {
					
					// should we retain this?
					[nextPhotos addObject: [tempphotos objectAtIndex: i3] ];
					}
				
				}
			
			[tempphotos release];
			[tokenXML release];
			
	} else {
		NSLog(@"There was an error getting user's photos..");
		
		if ([tokenXML rootElement] name] isEqualToString:@"error_response"]) {
			// error... output the msg
			
			[[NSArray *tempArray = tokenXML rootElement] elementsForName:@"error_msg"];
			
			if ([tempArray objectAtIndex:0]) {
				[[NSString *tempString = [[NSString alloc] initWithString:tempArray objectAtIndex:0] stringValue;
				NSLog(@"xml error message: %@", tempString);
				[tempString release];
				}
			
			[tempArray release];
			
			}
		
		[tokenXML release];
		[tempphotos release];
		}
	[postArgs release];

}


// which in turn calls this method...

- (NSData *)fetchXMLData:(NSString *)post
{
	
	
	NSData *postData = [post dataUsingEncoding:NSASCIIStringEncoding allowLossyConversion:YES];

	NSString *postLength = [NSString stringWithFormat:@"%d", [postData length]];

	NSMutableURLRequest *request = [[NSMutableURLRequest alloc] init];
	[request setURL:[NSURL URLWithString:@"http://api.facebook.com/restserver.php"]];
	[request setHTTPMethod:@"POST"];
	[request setValue:postLength forHTTPHeaderField:@"Content-Length"];
	[request setValue:@"application/x-www-form-urlencoded" forHTTPHeaderField:@"Content-Type"];
	[request setHTTPBody:postData];
	[request setCachePolicy:NSURLRequestReloadIgnoringCacheData];
	connectionTimeoutInterval = 0.5;
	[request setTimeoutInterval:connectionTimeoutInterval];
	
	/*
	NSURLRequest *urlRequest = [NSURLRequest requestWithURL:theURL 
												cachePolicy:NSURLRequestReloadIgnoringCacheData
											timeoutInterval:connectionTimeoutInterval];
	*/
	NSHTTPURLResponse *xmlResponse;  //not used right now
	NSError *fetchError;
	NSData *xmlData = [NSURLConnection sendSynchronousRequest:request
											returningResponse:&xmlResponse
														error:&fetchError];
	
	
	if(fetchError != NULL)
	{
	[request release];	
	return nil;
	}
	
	[request release];
	
	return xmlData;
}






I don't *think* there are any leaks in the code... but MallocDebug says otherwise. It's a screensaver, so it's running for a long time - and thus memory leaks are a big issue.

I've tried to use curlHandle - but I couldn't get it to link properly. Could this have something to do with it being a screensaver?

Any help would be appreciated...

Thanks,
Alexandre


----
There's a well-known bug that     sendSynchronousRequest: will leak when used from multiple threads. The workaround is to use the asynchronous delegate-based NSURLConnection methods instead.

----
Hmm... OK, thanks. I'm assuming Apple is aware of this bug?

Alexandre
----
I wouldn't asume that, I did submit a bug that was also well known, but it toke a long time before it became a duplicate. I would say submit it anyway, it will also get more and more awareness.

RvA

----
It looks like it's pretty well known, both in and outside of Apple:

http://www.google.com/search?client=safari&rls=en&q=nsurlconnection+leak+site:cocoabuilder.com&ie=UTF-8&oe=UTF-8

However, filing a bug wouldn't hurt; Apple uses the number of bugs filed about a particular problem to help determine which ones should be fixed first.

----

Ok, so right now I'm trying to set up the asynchronous connection. I can do it fine when I run it from the main thread, but I run into problems when I try doing it from a thread that I have detached from the main. The reason I need to do it is because it's a screensaver, and if I do it in the AnimateOneFrame method the animation lags. I've created a new object "Downloader" to use as the delegate. I can set some variables in the Downloader instance that I create so that I know what to do with the data after It's been received (I do several different types of requests, sometimes at the same time). 

Anyways, here's the code:

    

// this is in a different thread, in a loop... so the thread does not exit. Also, this doesn't get called again until the connection is finished (there's a variable called gettingMorePhotos that is set to true to avoid calling two at once).

Downloader *photoDownloader = Downloader alloc] initWithType:@"userPhotos" andMainClass:self];

[photoDownloader setString:[[filteredFriends objectAtIndex:0] uid;
					
NSURLConnection *theConnection=[[NSURLConnection alloc] initWithRequest:request delegate:photoDownloader];

if (theConnection) {
						
// Create the NSMutableData that will hold
// the received data
[photoDownloader createData];
						
NSLog(@"Connection created");
						
} else {
						
NSLog(@"could not create connection..");
// inform the user that the download could not be made

}




This code works fine when it's not in a new thread. But when it is in a new thread, the Connection gets created, but the delegate methods do not get called. What's the deal here? What am I doing wrong?

Thanks,
Alexandre

