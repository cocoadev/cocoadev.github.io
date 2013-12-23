---
layout: page
title: AddressBookQuestion
---

I am working on a project that involves syncing an SQLite database of persons with Address Book.  I have read (and re-read) the Address Book Programming Guide at the Apple Developer site, and I am pretty darn sure that I have my code correct.  I will post it below anyway.

At this point, adding new persons to the Address Book works, but I continue to receive the following messages in the console.  They are repeated multiple times, and these are just examples.  For a database of 684 persons, the text of the console log makes a 164kb TextEdit document.

     
2009-03-26 07:31:03.439 AddressBookSync[8980:10b] There's already an instance of AddressBookSync running.
2009-03-26 07:31:05.977 AddressBookSync[8981:10b] AddressBookSync (client id: com.apple.AddressBook) error: Exception running AddressBookSync: Session <ISyncConcreteSession: 0x189f80> cancelled. you referenced the following records (in a relationship) but did not actually push them: (
    "DAE63198-989B-4A31-9C68-7B804907B0A5",
    "1964C12E-D95F-4261-B9F2-A910BABCF443",
......
......
2009-03-26 07:32:04.031 iClerk[8977:10b] AddressBookSync exited with 1
 

Here is the code that I am using to add persons to the Address Book.  The initializer also sets pointers to Address Book Groups, and I have not included that code to keep this brief.
     
-(id)initICAB
{
	NSLog(@"initICAB called");
	self = [super init];
	addressBook = [ABAddressBook sharedAddressBook];
	return self;
}

-(void)addMemberToAddressBook:(NSDictionary *)newMember
{
	//First, we have to take the full name from the dictionary and split it up into Last, First
	NSArray *memNames = newMember objectForKey:@"[[FullName"] componentsSeparatedByString:@", "];
	
	member_Note = [self createMemberNote:newMember];
	newPerson = [[ABPerson alloc] init];
	
	[newPerson setValue:[memNames objectAtIndex:1] forProperty:kABFirstNameProperty];
	
	[newPerson setValue:[memNames objectAtIndex:0] forProperty:kABLastNameProperty];

	[newPerson setValue:member_Note forProperty:kABNoteProperty];
		
	//Address information is also MultiValue properties. See Address Book Cocoa sample code
	
	homeAddr = [NSMutableDictionary dictionary];
    [homeAddr setObject:[newMember objectForKey:@"Address"] forKey: kABAddressStreetKey];
    [homeAddr setObject:[newMember objectForKey:@"City"] forKey: kABAddressCityKey];
    [homeAddr setObject:[newMember objectForKey:@"State"] forKey: kABAddressStateKey];
    [homeAddr setObject:[newMember objectForKey:@"Zip"] forKey: kABAddressZIPKey];
	
	//Now, need to add this stuff to the new person
	multiValue = [[ABMutableMultiValue alloc] init];
    [multiValue addValue: homeAddr withLabel: kABAddressHomeLabel];
	
    // Set value in record for kABAddressProperty.
    [newPerson setValue: multiValue forProperty: kABAddressProperty];
	[multiValue release];
	
	//The phone numbers are all MultiValue properties.  See Address Book Cocoa sample code
	
	multiValue = [[ABMutableMultiValue alloc] init];
	[multiValue addValue:[newMember objectForKey:@"Phone01"] withLabel: kABPhoneHomeLabel];
    [multiValue addValue:[newMember objectForKey:@"Phone02"] withLabel: kABPhoneMobileLabel];
   	
    // Set value in record for kABPhoneProperty.
    [newPerson setValue: multiValue forProperty: kABPhoneProperty];
  
	[multiValue release];
		
	//Email is also a multiValue property, but no sample code exists, will need to extrapolate from  other examples
	
	multiValue = [[ABMutableMultiValue alloc] init];
	[multiValue addValue:[newMember objectForKey:@"Email"] withLabel:kABEmailHomeLabel];
	
	[newPerson setValue:multiValue forProperty:kABEmailProperty];
	[multiValue release];
	
	// Add record to the Address Book -- This is from the Address Book Cocoa Example code
    
	[addressBook addRecord:newPerson];
	[mainWardGroup addMember:newPerson];
	[addressBook save];
	
	[newPerson release];
		
}
 

As you can see by my commentary, I have done my best to use the code patterns recommended by Apple in both their Programming Guide, and in their Address Book Cocoa Example Code Project.  Everything actually works; the Address Book contains all the people, in the proper group, and with their personal information in the proper places.  But this data in the console log is really bugging me (pun intended) and I cannot figure out what else to do to investigate those warnings and make the system happy.

Does anyone have any experience with this sort of thing?

----

When you call the method above to add a new contact, has the group (i.e. mainWardGroup) that you are adding the contact to already been saved earlier to the Address Book database via the -addRecord: method?

----
Yes.  As part of the initialization of the class that handles all the AB interaction.  First, the initializer looks to see if the appropriate Group already exists, and if not, this is performed:

    
	//This should only be called once, when the ward name does not already exist as the name of any ABGroup
		
		if (![groupNames containsObject:wardName])
		{
			NSArray *newGroupNames = [NSArray arrayWithObjects:wardName, [wardName stringByAppendingString:@" Moved Out"], nil];
			ABGroup *newGroup;
			for (NSString *newGroupNm in newGroupNames)
			{
				newGroup = [[ABGroup alloc] init];
				[newGroup setValue:newGroupNm forProperty:kABGroupNameProperty];
				[addressBook addRecord:newGroup];
				[newGroup release];
				[addressBook save];
			}
			
			groupsFound = [self searchForWardGroup:wardName]; //Need to do this again, protecting against first run issues
		}


This creates two groups, the main group, and Moved Out group.  If, on the other hand, the Groups are already present in Address Book, the initializer goes directly to this section:
    
	//Now, we need to assign the groups to the ABGroup pointers.  In either situation, groupsFound should now be two items
		//I just don't know which order the groups will be listed in the array
		
		for (ABGroup *wg in groupsFound)
		{
			if ([[wg valueForKey:kABGroupNameProperty] isEqualToString:wardName]) 
			{
				mainWardGroup = wg;
			}
			else
			{
				movedWardGroup = wg;
			}
		}

