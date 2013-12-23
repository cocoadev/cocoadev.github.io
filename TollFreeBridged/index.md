---
layout: page
title: TollFreeBridged
---

The following classes are toll-free bridged:

*Foundation
*General/NSArray = General/CFArray
*General/NSMutableArray = General/CFMutableArray
*General/NSAttributedString = General/CFAttributedString (Mac OS X only, not iOS)
*General/NSMutableAttributedString = General/CFMutableAttributedString (Mac OS X only, not iOS)
*General/NSCalendar = General/CFCalendar
*General/NSCharacterSet = General/CFCharacterSet
*General/NSMutableCharacterSet = General/CFMutableCharacterSet
*General/NSData = General/CFData
*General/NSMutableData = General/CFMutableData
*General/NSDate = General/CFDate
*General/NSDictionary = General/CFDictionary
*General/NSMutableDictionary = General/CFMutableDictionary
*General/NSError = General/CFError
*General/NSLocale = General/CFLocale
*General/NSNumber = General/CFNumber
*General/NSNumber *partially bridged with* General/CFBoolean, for booleans only
*General/NSTimer = General/CFRunLoopTimer
*General/NSTimeZone = General/CFTimeZone
*General/NSSet = General/CFSet
*General/NSMutableSet = General/CFMutableSet
*General/NSStream = General/CFStream
*General/NSInputStream = General/CFReadStream
*General/NSOutputStream = General/CFWriteStream
*General/NSString = General/CFString
*General/NSMutableString = General/CFMutableString
*General/NSUrL = CFURL
*General/AppKit
*General/NSFont = General/CTFont
*General/NSFontDescriptor = General/CTFontDescriptor
*Disc Recording
*General/DRBurn = General/DRBurnRef
*General/DRErase = General/DREraseRef
*General/DRDevice = General/DRDeviceRef
*General/DRTrack = General/DRTrackRef
*General/DRFile = General/DRFileRef
*General/DRFolder = General/DRFolderRef (starting with 10.3)
*Address Book
*General/ABAddressBook = General/ABAddressBookRef
*General/ABGroup = General/ABGroupRef
*General/ABMultiValue = General/ABMultiValueRef
*General/ABMutableMultiValue = General/ABMutableMultiValueRef
*General/ABPerson = General/ABPersonRef
*General/ABRecord = General/ABRecordRef
*General/ABSearchElement = General/ABSearchElementRef


The following classes are ***not*** toll-free bridged:

*General/NSBundle and General/CFBundle
*General/NSCountedSet and General/CFBag
*General/NSDateFormatter and General/CFDateFormatter
*General/NSNumberFormatter and General/CFNumberFormatter
*General/NSHost and General/CFHost
*General/NSNotificationCenter and General/CFNotificationCenter
*General/NSRunLoop and General/CFRunLoop
*General/NSSocket and General/CFSocket
*General/NSUserDefaults and General/CFPreferences
*General/AppKit
*General/NSColor, General/CIColor, and General/CGColor
*General/NSColorSpace and General/CGColorSpace
*General/NSFont and General/CGFont
*General/NSGradient and General/CGGradient
*General/NSImage and General/CGImage


See also General/TollFreeBridging.

----

If you wonder whether an General/ObjC class is bridged and can't find docs, send an object of the class the _cfTypeID message.  If you get back '1', it isn't bridged.  For more detail see General/HowToCreateTollFreeBridgedClass.
