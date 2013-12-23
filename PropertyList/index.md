---
layout: page
title: PropertyList
---



A PropertyList is a file--generally ending in .plist--which contains, guess what, properties. They are commonly used by NSUserDefaults to store preferences. For more information, see the Property List Programming Guide for Cocoa:

http://developer.apple.com/documentation/Cocoa/Conceptual/PropertyLists/index.html

Property lists can only hold the following data types:


*NSDictionary
*NSArray
*NSString
*NSNumber
*NSDate
*NSData


Additionally, NSDictionary keys must be strings and cannot be any other type. Old-Style ASCII property lists, such as what you get from sending     -description to an NSDictionary, cannot properly represent NSNumber or NSDate objects.

Please note that no other classes are allowed. This includes your custom classes as well as all other Cocoa classes. There is no way to modify a class to make it work in a PropertyList, instead you must manually convert its data to a PropertyList data type.

Property lists are typically unarchived into standard Foundation objects with initWithContentsOfFile: methods. You can also convert a string containing a plist into an object using NSString's propertyList message. Standard Foundation objects can often be written to file as property lists using writeToFile:atomically:. For total control over creation and reading of PropertyList<nowiki/>s, use the NSPropertyListSerialization class.

Note that reading a mutable object from file does not create a deep mutable copy of your plist data. For example, if you have an array of dictionaries like:

    
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist SYSTEM "file://localhost/System/Library/DTDs/PropertyList.dtd">
<plist version="0.9">
    <array>
        <dict>
            <key>Name</key>
            <string>Herman</string>
            <key>Address</key>
            <string>1313 Mockingbird Lane</string>
        </dict>
    </array>
</plist>


and you instantiate a mutable array using this code snippet:

    
NSMutableArray *friends = [NSMutableArray arrayWithContentsOfFile:path];


The resulting array will be an NSMutableArray, but the array's elements will be normal NSDictionary objects. If you need to do a deeply mutable copy (i.e., create a mutable array with mutable dictionaries) you need to use CoreFoundation's CFPropertyListCreateFromXMLData function:

    
NSString * path = @"foo.plist"
NSData * data = NULL;
CFStringRef errStr = NULL;
NSMutableArray * friends;

data = [NSData dataWithContentsOfFile:path];
if ( ! data )
{
	NSLog(@"An error occurred: couldn't read from %@", path);
}
else
{
	friends = (NSMutableArray *) CFPropertyListCreateFromXMLData(
		kCFAllocatorDefault,
		(CFDataRef) data, 
		kCFPropertyListMutableContainersAndLeaves, 
		&errStr);
	if ( errStr != NULL ) 
	{
		NSLog(@"An error occurred: %@", (NSString*)errStr); // Edited "errstr" to "errStr" for those wishing to copy/paste
		CFRelease(errStr);
	}
	else
	{
		NSLog(@"Property list loaded succesfully!");
	}
}


Here friends will be a mutable array of mutable dictionaries.

Foundation's PropertyList API is scattered about. CoreFoundation's PropertyList API can be found here:

/System/Library/Frameworks/CoreFoundation.framework/Headers/CFPropertyList.h

----

See also AsciiPropertyLists, XmlPropertyLists, and ArchivingToAndUnarchivingFromPropertyLists.

----
**Discussion**

I changed the NSNull warning to a general warning about all Cocoa classes. In my opinion the NSNull thing was too specific, and mentioning that *all* other classes are prohibited still catches that case and is more useful for other things. Thoughts? -- MikeAsh

*NSNull objects are more likely to sneak into a property list than a table view. I'm not sure if it is more helpful to consider NSNull unworthy of special mention.*

----
More likely than an NSSet? More likely than an NSAttributedString or an NSColor or an NSFont?

As far as I can tell the only reason for the specific mention of NSNull is because the creator of NSNullInPropertyList had it happen to him and wanted to warn the world. While this is nice, I think a more generalized warning is considerably more useful. Generalization is critical to programming; when you encounter a specific problem, the best way to learn from it is to figure out the *general* class of problems that it represents, then avoid that entire class of problems in the future. -- MikeAsh

