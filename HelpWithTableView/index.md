---
layout: page
title: HelpWithTableView
---

I'm trying to get a simple NSTableView implementation to work, but I'm beginner and VERY confused.

Can anyone give me a simple example of a NSTableView which takes his values from an NSMutableArray with String objects in it...

I have a NSTableView and a NSTextField. What I want to implement is whenever I type a word in the field and hit "Enter", it adds the content of the field into the NSTableView...

What gives me most trouble is the identifier I have to specify for the NSTableColumn parameter.
I don't know what is an identifier.

Thanks for the help...

----

here are some examples from my app renamer,

    
- (id)tableView:(NSTableView *)tableView objectValueForTableColumn:(NSTableColumn *)tableColumn row:(int)row
{
	NSString	*identifier=[tableColumn identifier];
	
	if ([identifier isEqualToString:@"Option"])
		return [dataSourceList objectAtIndex:row];
             
        if ([identifier isEqualToString:@"Orignal"]) 
		return [dataSourceListOrignal objectAtIndex:row];                
        if ([identifier isEqualToString:@"Proposed"])
		return [dataSourceListProposed objectAtIndex:row];
	   	
	   return nil;
}

- (BOOL)tableView:(NSTableView *)tableView shouldSelectRow:(int)row
{
	if(tableView==theTableView) {
		NSString	*itemString=[dataSourceList objectAtIndex:row];

		if ([itemString isEqualToString:@"File"]) {
			[theMainViewOutlet setContentView: filesView];
    }
    if(tableView==previewTableView) {
    }
	return YES;
}


hope it helps, reply if you dont understand any of it, and i will try and explain it.

----

By the time you wrote your example, I got one from cocoadevcentral.com. All I had to do is making a category, which I don't understand fully, but it works. The code goes like this :

    
@implementation NSArray (NSTableDataSource)

- (id)	tableView:(NSTableView *) aTableView
        objectValueForTableColumn:(NSTableColumn *) aTableColumn
        row:(int) rowIndex
{
    return [self objectAtIndex:rowIndex];
}

- (int)numberOfRowsInTableView:(NSTableView *)aTableView
{
  return [self count];
}

@end



If you have information about categories or you can explain it yourself, feel free to do so.

Now, I have another thing to learn about Table Views. I don't want the user to be able to change the data in my NSTableView. I would prefer that the user could not even select any rows. Is it possible ?

----

The identifier name is set in InterfaceBuilder und should be unique for every column. It's great for "key-value coding". An example:
suppose you have a class called Person, holding two NSStrings, one for the first name, the second for the last name

    
// Person.h
#import <Foundation/Foundation.h>


@interface Person : NSObject {
    NSString	*_lastName;
    NSString	*_firstName;

}

- (NSString *)lastName;
- (void)setLastName:(NSString *)newLastName;
- (NSString *)firstName;
- (void)setFirstName:(NSString *)newFirstName;

@end


// Person.m
#import "Person.h"


@implementation Person
- (NSString *)lastName {
    return _lastName;
}

- (void)setLastName:(NSString *)newLastName {
    if (_lastName != newLastName) {
        NSString *oldValue = _lastName;
        _lastName = [newLastName retain];
        [oldValue release];
    }
}

- (NSString *)firstName {
    return _firstName;
}

- (void)setFirstName:(NSString *)newFirstName {
    if (_firstName != newFirstName) {
        NSString *oldValue = _firstName;
        _firstName = [newFirstName retain];
        [oldValue release];
    }
}


@end

Now, when you set identifier of column one to lastName and for the second column to firstName, it will be automatically binded to the right values of your person class.
--BobC

----

Yes, I understand the basics; that's easier than I though. Thanks Bob...

