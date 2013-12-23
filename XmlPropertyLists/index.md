---
layout: page
title: XmlPropertyLists
---

Mac OS X uses XML as its General/PropertyListFormat. The format looks like this:

    
<!-- This is a comment -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist SYSTEM "file://localhost/System/Library/General/DTDs/General/PropertyList.dtd">
<plist version="0.9">
<dict>
        <key>food</key>
        <string>pizza</string>
        <key>restaraunts</key>
        <array>
                <string>pizzahut</string>
                <string>dominoes</string>
                <string>donatos</string><!-- :-) -->
        </array>
</dict>


You can typically read and write General/XmlPropertyLists using Foundation objects' -initWithContentsOfFile: and -writeToFile:atomically: methods.

For example, if you have a property list "General/ExampleArray.plist" in your project whose root is an array, you can read it with this snippet:
    
General/NSArray array* = General/[NSArray arrayWithContentsOfFile: 
  General/[[NSBundle bundleForClass:[self class]] pathForResource:@"General/ExampleArray" ofType:@"plist"]];




Mac OS X supports an older property list format known as General/AsciiPropertyLists.

You can convert an XML property list to the old ascii format with the 'pl' command (although some types are not supported in the old format). General/TextExtras will also allow a menu option to convert back and fourth.

See also General/PropertyList.
