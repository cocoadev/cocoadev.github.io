---
layout: page
title: AddressBookNameOrder
---

I'm trying to write an app that is localized for both Japanese and English (for now), and I need to construct full names for people in the AddressBook.

The problem is that in Japanese and who knows what else, last names go before first names in a full name.

Okay, so panther provides information as to ordering in the ABPersonFlags property of an ABPerson, but I can't figure out how to interpret the info.

----

The ABPersonFlags property is used to access the following settings:

* kABDefaultNameOrdering - Default name ordering (whether a person's first name or last name is displayed first) the Address Book application.
* kABFirstNameFirst - First name is displayed first in Address Book.
* kABLastNameFirst - Last name is displayed first in Address Book.
* kABNameOrderingMask - Used in conjunction with kABDefaultNameOrdering, kABFirstNameFirst, and kABLastNameFirst to determine name order


----

Figured it out.  For anyone who else who wonders, this code will compute the name order for a person.

    
 ABAddressBook *addressBook = [ABAddressBook sharedAddressBook];
 ABPerson *person = addressBook people] objectAtIndex:0];
 BOOL lastNameGoesFirst;
 
 int nameOrderFlags = [[person valueForProperty:kABPersonFlags] intValue] & kABNameOrderingMask;
 
 if (nameOrderFlags == kABDefaultNameOrdering)
 {
     nameOrderFlags = [addressBook defaultNameOrdering];
 }
             
 if (nameOrderFlags == kABLastNameFirst)
 {
     lastNameGoesFirst = YES;
 }
 else
 {
     lastNameGoesFirst = NO;
 }


----

Apparently this is what you need to do in order to display a [[ABPerson object. Dunno why this isn't in the API.

    
 @interface ABPerson (MyABPersonAddition)
 - (NSString *)displayName_;
 @end
 
 @implementation ABPerson (MyABPersonAddition)
 - (NSString *)displayName_
 {
 	int personFlags = [[self valueForProperty:kABPersonFlags] intValue];
 	int showAs = (personFlags & kABShowAsMask);
 	if (showAs == kABShowAsPerson)
 	{
 		NSString *title = [self valueForProperty:kABTitleProperty];
 		NSString *lastName = [self valueForProperty:kABLastNameProperty];
 		NSString *firstName = [self valueForProperty:kABFirstNameProperty];
 		NSString *middleName = [self valueForProperty:kABMiddleNameProperty];
 		NSString *suffix = [self valueForProperty:kABSuffixProperty];
 		
 		int nameOrdering = (personFlags & kABNameOrderingMask);
 		if (nameOrdering == kABDefaultNameOrdering)
 			nameOrdering = [[ABAddressBook sharedAddressBook] defaultNameOrdering];
 		
 		NSMutableString *outDisplayName = [NSMutableString string];
 		if (title)
 			[outDisplayName appendFormat:@"%@ ", title];
 		if ((nameOrdering == kABLastNameFirst) && lastName)
 			[outDisplayName appendFormat:@"%@ ", lastName];
 		if (firstName)
 			[outDisplayName appendFormat:@"%@ ", firstName];
 		if (middleName)
 			[outDisplayName appendFormat:@"%@ ", middleName];
 		if ((nameOrdering != kABLastNameFirst) && lastName)
 			[outDisplayName appendFormat:@"%@ ", lastName];
 		if (suffix)
 			[outDisplayName appendFormat:@"%@ ", suffix];
 		// delete trailing space
 		[outDisplayName deleteCharactersInRange:NSMakeRange([outDisplayName length]-1, 1)];
 		return outDisplayName;
 	}
 	else
 	{
 		return [self valueForProperty:kABOrganizationProperty];
 	}
 }
 @end

