---
layout: page
title: SortingExampleUsingNSComparisonResult
---

Here is a simple example of sorting an array using NSC**'omparisonResult in the context of displaying it in an NSTableView:

This sorts a list of student grades by either name or by their grade percentages.  We assume you are familiar with actually creating, displaying and updating a NSTableView object through its NSMutableArray contents.  Assume that your NSTableView is fed by a NSMutableArray containing Student objects and you want to sort them by either grade or name.

Here is a piece of the interface code:

    
@interface Student : NSObject
{
	NSString	*lastName;
	NSString	*firstName;
	float		result;
	NSString	*grade;
}

/* definitions and other methods for Student objects would be here... */

// Comparator functions for sorting
- (NSComparisonResult)compareNames:(Student *)anotherStudent;
- (NSComparisonResult)compareResults:(Student *)anotherStudent;


We define two methods (compareNames: & compareResults:) whose sole responsibility is to compare one student object (self) to another student object (anotherStudent).  The implementation for these two methods looks like this:

    
@implementation Student
/* Other methods implementations here... */

// Comparison routines
- (NSComparisonResult)compareNames:(Student *)anotherStudent
{
	return [lastName caseInsensitiveCompare:[anotherStudent lastName]];
}

- (NSComparisonResult)compareResults:(Student *)anotherStudent
{
	if (result < [anotherStudent result])
		return NSOrderedAscending;
	else if (result > [anotherStudent result])
		return NSOrderedDescending;
	else
		return NSOrderedSame;
}


compareNames: from above compares two NSString objects and returns the result of the compare.  compareResults: compares two float objects the same way.  So how do I sort the entire array in Cocoa?  It can actually be done in a single line of code (actually one for each column) and is shown below:

    
	if ([sortByName intValue] == 1)
		sortedArray = [students sortedArrayUsingSelector:@selector(compareNames:)];
	else
		sortedArray = [students sortedArrayUsingSelector:@selector(compareResults:)];


"students" is an NSMutableArray object containing a bunch of (Student) objects.  "sortedArray" is an NSArray or an NSMutableArray object containing the same objects as students but sorted in the order you want.  You can now either use sortedArray in your table view or just assign it back to the students if you choose. Then send a "reloadData" to your NSTableView object.  That is all there is to it.

--JohnathanSteere

