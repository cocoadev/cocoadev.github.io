---
layout: page
title: ScreenSaverDefaultsCrashingProblem
---



Hi There,

I'm getting a BAD_ACCESS crash when I try and put an object into my ScreenSaverDefaults. I get the feeling its a problem with the way I've been retaining objects and such. Here's what I've done: I created a "Friend" object, as show here.

    

@interface Friend : NSObject {
	NSString	*firstName;
	NSString	*lastName;
	NSString	*uid;
	NSNumber	*active;
	float		result;
}

-(id)init;
- (void)setFirstName:(NSString *)aFirstName;
- (NSString *)firstName;
- (void)setLastName:(NSString *)aLastName;
- (NSString *)lastName;
- (void)setUid:(NSString *)aUid;
- (NSString *)uid;
- (void)setActive:(NSNumber *)aNumber;
- (NSNumber *)active;
-(void)setResult:(float)aFloat;
-(float)result;
-(NSString *)name;

@implementation Friend

-(id)init
{
self = [super init];
return self;
}

-(NSString *)name
{
	NSMutableString *Name = [[[NSMutableString alloc]  initWithString:@""] autorelease];
	NSArray *Arguments = [NSArray arrayWithObjects:[self firstName], [self lastName], nil];
	[Name appendString:[Arguments componentsJoinedByString:@" "]];
	return Name;
}

-(void)setFirstName:(NSString *)aFirstName
{
	aFirstName = [aFirstName copy];
	[aFirstName release];
	firstName = aFirstName;
}

-(NSString *)firstName
{
	return firstName;
}

/// and so forth for the other implementation methods



Then, I parse some xml of friends, and create a Mutable Array of friend objects. Check it out below. The code loops through the friend nodes of the xml, creates a friend object, and then adds it to the array..

    

// friends array has already been created when I init the screensaver...
friends = [[NSMutableArray alloc] initWithCapacity:100];


// when i call the getFriends method...

for (i = 0; i < tokenXML rootElement] childCount]; i++) {
	
	aNode = [[tokenXML rootElement] childAtIndex: i];
	
	Friend *tempFriend = [[Friend alloc] init];
	
	// ok, now loop through nodes
	for (i2=0; i2 < [aNode childCount]; i2++) {
		
		// create a friend object
		
		
		
		
		
		
		if ([[[aNode childAtIndex:i2] name] isEqualTo:@"first_name"]) {
			/// add to object
			
			[[NSString* tempString = [[NSString alloc] initWithString:aNode childAtIndex:i2] stringValue;
			
			[tempFriend setFirstName: [tempString retain]];
			
			[tempString release];
			
			}
		
		if ([aNode childAtIndex:i2] name] isEqualTo:@"last_name"]) {
			/// add to object
			
			[[NSString* tempString = [[NSString alloc] initWithString:aNode childAtIndex:i2] stringValue;
			
			[tempFriend setLastName: [tempString retain]];
			
			[tempString release];
			
			}
			
		if ([aNode childAtIndex:i2] name] isEqualTo:@"uid"]) {
			/// add to object
			
			[[NSString* tempString = [[NSString alloc] initWithString:aNode childAtIndex:i2] stringValue;
			
			[tempFriend setUid: [tempString retain]];
			
			[tempString release];

			
			}
		int tempInt;
		tempInt = 0;
		[tempFriend setActive:[NSNumber numberWithInt:tempInt]];
		
		
		
		
		
		}
	
	// ok, now check if we already have this friend
	BOOL hasFriend;
	hasFriend = NO;
	for (i3 = 0; i3 < [friends count]; i3++) {
		if ([friends objectAtIndex:i3] uid] isEqualToString: [tempFriend uid) {
			// we already have it
			hasFriend = YES;
			}
		}

	if (hasFriend == NO) {
		
		// add it to the friends array
		[friends addObject: [tempFriend retain]];
		}
	
	[tempFriend release];
	
	
	}







Then I do some sorting...

    
 lastNameDescriptor=[[[NSSortDescriptor alloc] initWithKey:@"lastName" 

									ascending:YES
									selector:@selector(caseInsensitiveCompare:)] autorelease];

sortDescriptors=[NSArray arrayWithObject:lastNameDescriptor];

sortedArray=friends sortedArrayUsingDescriptors:sortDescriptors] retain];

[friends release];

friends = [[[[NSMutableArray alloc] initWithArray: sortedArray];



Then, after all that, I try to add the friends array to the defaults, and that's where I get the crash:

    

[defaults setObject:friends forKey:@"friends"];
[defaults synchronize];




Anyways, I really feel like it has something to do with the way I'm retaining objects and such. Any ideas?

Thanks,
Alexandre

----
Your crash is almost certainly due to a MemoryManagement problem, your code is full of leaks and it wouldn't surprise me if there were over-releases too. Read up on MemoryManagement and fix your code, and if you need help finding the specific object that's being over-released then look up NSZombieEnabled.

----
Could you give any specific examples as to where I have gone wrong. Unfortunately it's difficult for me to debug as it's a ScreenSaver - NSZombieEnabled doesn't appear to work.

----
Yes, your retains and release aren't balanced. For example, you have a bunch of lines like     [tempFriend setUid: [tempString retain]] and they will all cause a leak.

NSZombieEnabled will work if you create a custom executable in your project and set it to System Preferences, or better yet to SaverLab.

Update: whups, I was wrong. You do indeed have a lot of MemoryManagement errors but they aren't causing your problem here. Your problem is that user defaults can only contain PropertyList objects, and you're trying to store a bunch of custom classes in it.

----
Ok, thanks..

So basically I can only store Arrays in NSUserDefaults, or dictionaries... Is that correct? (At least for the kinda of structured data I'm trying to store). Can I have an array of Dictionary Objects?

----
The PropertyList page has been updated with a comprehensive list of allowed data types and links to more information.

