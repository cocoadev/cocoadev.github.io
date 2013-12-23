---
layout: page
title: FileFormat
---

I am using some custom-built objects in an array and would like to save the array as a file. Do I have to write my own file format for this or will NSObject's methods work fine or maybe do I need to override the description method of the NSObject class? Any suggestions?

Thanks
Saad R. Abbasi

----

Make your object conform to the NSCoding protocol and you can use NSArray's     writeToFile method

----

To use     writeToFile:atomically:, the contents of the array must be property list objects. (That is, Cocoa containers, strings, numbers, dates, etc., no custom objects.) If you want to save your custom objects, you can either create a property list from them and save that, or you can make your objects conform to NSCoding and use NSArchiver/NSUnarchiver (or the Keyed variants) to save them.

