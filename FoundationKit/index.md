---
layout: page
title: FoundationKit
---



The FoundationKit is a set of classes first developed by NextComputer, and now by AppleComputer, for core application services.

It provides things like collections (such as Arrays and Dictionaries), strings and string formatting, and other miscellaneous primitive (but essential) support classes. 

----

**Note:** Please don't go to the trouble of copying Apple's complete docs for each and every class - that's more than we need. :)

You might want to link to the corresponding page on the AppleComputer developer site and add some sample code and gotchas that can't be found there. (Plus: Fill in that "Description forthcoming." :) )

----
**Note:** Cocoa is available to both Objective-C and Java. Documentation for classes is available for each language except where noted by a (Java) or (ObjC).

NSMutableArray is ObjC only?! I don't believe that! -- KritTer [ why not? Java's got the Java libraries already... why wouldn't you just use com.whatever.array? ] Because according to Apple's docs, only NSEnumeratedSet is ObjC only. And because NSArray is in Java. Have they lied to us? They even have documentation for NSMutableArray in Java on their site! http://developer.apple.com/techpubs/macosx/Cocoa/Reference/Foundation/Java/Classes/NSMutableArray.html -- KritTer

----
What are you guys talking about? The (objc) means that the page doesn't have anything about java, it doesn't mean it's obj-c only. You are right, NSEnumeratedSet is the only Obj-C only thing.

*So, if I understand you correctly, all the (ObjC) notes below should be removed except for NSEnumeratedSet? And any ObjC-only classes added since then?*
----


== FoundationKit Overviews ==
    
 FoundationCollections (Arbitrary storage classes)


== FoundationKit Classes ==
    
 NSAffineTransform (10.4)
 NSAppleEventDescriptor (ObjC)
 NSAppleEventManager (ObjC)
 NSAppleScript
 NSArchiver
 NSArray
 NSAssertionHandler (ObjC)
 NSAttributedString
 NSAutoreleasePool
 NSBundle
 NSCachedURLResponse (10.4)
 NSCalendar (10.4)
 NSCalendarDate (ObjC)
 NSCharacterSet
 NSClassDescription
 NSCloneCommand (ObjC)
 NSCloseCommand (ObjC)
 NSCoder
 NSComparisonPredicate (10.4)
 NSCompoundPredicate (10.4)
 NSConditionLock (ObjC)
 NSConnection (ObjC)
 NSCountCommand (ObjC)
 NSCountedSet (ObjC)
 NSCreateCommand (ObjC)
 NSData
 NSDate
 NSDateComponents (10.4)
 NSDateFormatter (ObjC)
 NSDecimalMappingBehavior (Java)
 NSDecimalNumber (ObjC)
 NSDecimalNumberHandler (ObjC)
 NSDeleteCommand (ObjC)
 NSDeserializer (ObjC)
 NSDictionary
 NSDirectoryEnumerator (ObjC)
 NSDistantObject (ObjC)
 NSDistantObjectRequest (ObjC)
 NSDistributedLock (ObjC)
 NSDistributedNotificationCenter
 NSEnumerator
 NSError (10.3)
 NSException
 NSExistsCommand (ObjC)
 NSExpression (10.4)
 NSFileHandle (ObjC)
 NSFileManager (ObjC)
 NSFormatter
 NSFormatter.FormattingException (Java)
 NSFormatter.ParsingException (Java)
 NSGetCommand (ObjC)
 NSGregorianDate (Java)
 NSGregorianDate.IntRef (Java)
 NSGregorianDateFormatter (Java)
 NSHost (ObjC)
 NSHTTPCookie (10.4)
 NSHTTPCookieStorage (10.4)
 NSHTTPURLResponse (10.4)
 NSIndexPath (10.4)
 NSIndexSet (10.3)
 NSIndexSpecifier (ObjC)
 NSInputStream (10.3)
 NSInvocation (ObjC)
 NSKeyedArchiver (10.2)
 NSKeyedUnarchiver (10.2)
 NSLocale (10.4)
 NSLock (ObjC)
 NSLogicalTest (ObjC)
 NSMachBootstrapServer (ObjC)
 NSMachPort (ObjC)
 NSMessagePort (ObjC)
 NSMessagePortNameServer (ObjC)
 NSMetadataItem (10.4)
 NSMetadataQuery (10.4)
 NSMetadataQueryAttributeValueTuple (10.4)
 NSMetadataQueryResultGroup (10.4)
 NSMethodSignature (ObjC)
 NSMiddleSpecifier (ObjC)
 NSMoveCommand (ObjC)
 NSMutableArray (ObjC)
 NSMutableAttributedString
 NSMutableCharacterSet
 NSMutableData
 NSMutableDictionary
 NSMutableIndexSet (10.3)
 NSMutablePoint (Java)
 NSMutableRange (Java)
 NSMutableRect (Java)
 NSMutableSet
 NSMutableSize (Java)
 NSMutableString (ObjC)
 NSMutableStringReference (Java)
 NSMutableURLRequest (10.2.7)
 NSNamedValueSequence (Java)
 NSNameSpecifier (10.2)
 NSNetService (ObjC - 10.2)
 NSNetServiceBrowser (ObjC - 10.2)
 NSNotification
 NSNotificationCenter
 NSNotificationQueue
 NSNull
 NSNumber (ObjC)
 NSNumberFormatter
 NSObject
 NSOutputStream (10.3)
 NSPathUtilities (Java)
 NSPipe (ObjC)
 NSPoint (Java)
 NSPort
 NSPortCoder (ObjC)
 NSPortMessage (ObjC)
 NSPortNameServer (ObjC)
 NSPositionalSpecifier (ObjC)
 NSPredicate (10.4)
 NSProcessInfo (ObjC)
 NSPropertyListSerialization (Java)
 NSPropertySpecifier (ObjC)
 NSProtocolChecker (ObjC)
 NSProxy (ObjC)
 NSQuitCommand (ObjC)
 NSRandomSpecifier (ObjC)
 NSRange (Java)
 NSRangeSpecifier (ObjC)
 NSRect (Java)
 NSRecursiveLock (ObjC)
 NSRelativeSpecifier (ObjC)
 NSRunLoop
 NSRuntime (Java)
 NSScanner (ObjC)
 NSScriptClassDescription (ObjC)
 NSScriptCoercionHandler (ObjC)
 NSScriptCommand (ObjC)
 NSScriptCommandDescription (ObjC)
 NSScriptExecutionContext (ObjC)
 NSScriptObjectSpecifier (ObjC)
 NSScriptSuiteRegistry (ObjC)
 NSScriptWhoseTest (ObjC)
 NSSelector (Java)
 NSSerializer (ObjC)
 NSSet
 NSSetCommand (ObjC)
 NSSize (Java)
 NSSocketPort (ObjC)
 NSSocketPortNameServer (ObjC)
 NSSortDescriptor (10.3)
 NSSpecifierTest (ObjC)
 NSSpellServer
 NSStream (10.3)
 NSString (ObjC)
 NSStringReference (Java)
 NSSystem (Java)
 NSTask (ObjC)
 NSThread (ObjC)
 NSTimer
 NSTimeZone
 NSUnarchiver
 NSUndoManager
 NSUniqueIDSpecifier (10.2)
 NSURL (ObjC)
 NSURLAuthenticationChallenge (10.2.7)
 NSURLCache (10.2.7)
 NSURLConnection (10.2.7)
 NSURLCredential (10.2.7)
 NSURLCredentialStorage (10.2.7)
 NSURLDownload (10.2.7)
 NSURLHandle (10.3)
 NSURLProtectionSpace (10.2.7)
 NSURLProtocol (10.2.7)
 NSURLRequest (10.2.7)
 NSURLResponse (10.2.7)
 NSUserDefaults
 NSValue (ObjC)
 NSValueTransformer
 NSWhoseSpecifier (ObjC)
 NSXMLDocument (10.4)
 NSXMLDTD (10.4) NSXmlDtd
 NSXMLDTDNode (10.4)
 NSXMLElement (10.4)
 NSXMLNode (10.4)
 NSXMLParser (10.3)
 

== FoundationKit Protocols (ObjC) ==
    
 NSCoding
 NSComparisonMethods
 NSCopying
 NSDecimalNumberBehaviors
 NSKeyValueBindingCreation
 NSKeyValueCoding
 NSLocking
 NSMutableCopying
 NSObjCTypeSerializationCallBack
 NSObject
 NSScriptKeyValueCoding
 NSScriptObjectSpecifiers
 NSScriptingComparisonMethods
 NSURLHandleClient


== FoundationKit Interfaces (Java) ==
    
 NSComparisonMethods
 NSKeyValueCoding 
 NSScriptingComparisonMethods
 NSScriptingKeyValueCoding


== FoundationKit Functions (ObjC) ==
http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Functions/FoundationFunctions.html

=== Assertions ===
    
 NSAssert
 NSAssert1
 NSAssert2
 NSAssert3
 NSAssert4
 NSAssert5
 NSCAssert
 NSCAssert1
 NSCAssert2
 NSCAssert3
 NSCAssert4
 NSCAssert5
 NSCParameterAssert
 NSParameterAssert


=== Bundles ===
    
 NSLocalizedString
 NSLocalizedStringFromTable
 NSLocalizedStringFromTableInBundle
 NSLocalizedStringWithDefaultValue


=== Byte Ordering ===
    
 NSConvertHostDoubleToSwapped
 NSConvertHostFloatToSwapped
 NSConvertSwappedDoubleToHost
 NSConvertSwappedFloatToHost
 NSHostByteOrder
 NSSwapBigDoubleToHost
 NSSwapBigFloatToHost
 NSSwapBigIntToHost
 NSSwapBigLongLongToHost
 NSSwapBigLongToHost
 NSSwapBigShortToHost
 NSSwapDouble
 NSSwapFloat
 NSSwapHostDoubleToBig
 NSSwapHostDoubleToLittle
 NSSwapHostFloatToBig
 NSSwapHostFloatToLittle
 NSSwapHostIntToBig
 NSSwapHostIntToLittle
 NSSwapHostLongLongToBig
 NSSwapHostLongLongToLittle
 NSSwapHostLongToBig
 NSSwapHostLongToLittle
 NSSwapHostShortToBig
 NSSwapHostShortToLittle
 NSSwapInt
 NSSwapLittleDoubleToHost
 NSSwapLittleFloatToHost
 NSSwapLittleIntToHost
 NSSwapLittleLongLongToHost
 NSSwapLittleLongToHost
 NSSwapLittleShortToHost
 NSSwapLong
 NSSwapLongLong
 NSSwapShort


=== Decimals ===
    
 NSDecimalAdd
 NSDecimalCompact
 NSDecimalCompare
 NSDecimalCopy
 NSDecimalDivide
 NSDecimalIsNotANumber
 NSDecimalMultiply
 NSDecimalMultiplyByPowerOf10
 NSDecimalNormalize
 NSDecimalPower
 NSDecimalRound
 NSDecimalString
 NSDecimalSubtract


=== Java Setup ===
    
 NSJavaBundleCleanup
 NSJavaBundleSetup
 NSJavaClassesForBundle
 NSJavaClassesFromPath
 NSJavaNeedsToLoadClasses
 NSJavaNeedsVirtualMachine
 NSJavaObjectNamedInPath
 NSJavaProvidesClasses
 NSJavaSetup
 NSJavaSetupVirtualMachine


=== Hash Tables ===
    
 NSAllHashTableObjects
 NSCompareHashTables
 NSCopyHashTableWithZone
 NSCountHashTable
 NSCreateHashTable
 NSCreateHashTableWithZone
 NSEndHashTableEnumeration
 NSEnumerateHashTable
 NSFreeHashTable
 NSHashGet
 NSHashInsert
 NSHashInsertIfAbsent
 NSHashInsertKnownAbsent
 NSHashRemove
 NSNextHashEnumeratorItem
 NSResetHashTable
 NSStringFromHashTable


=== HFS File Types ===
    
 NSFileTypeForHFSTypeCode
 NSHFSTypeCodeFromFileType
 NSHFSTypeOfFile


=== Map Tables ===
    
 NSAllMapTableKeys
 NSAllMapTableValues
 NSCompareMapTables
 NSCopyMapTableWithZone
 NSCountMapTable
 NSCreateMapTable
 NSCreateMapTableWithZone
 NSEndMapTableEnumeration
 NSEnumerateMapTable
 NSFreeMapTable
 NSMapGet
 NSMapInsert
 NSMapInsertIfAbsent
 NSMapInsertKnownAbsent
 NSMapMember
 NSMapRemove
 NSNextMapEnumeratorPair
 NSResetMapTable
 NSStringFromMapTable


=== Object Allocation/Deallocation ===
    
 NSAllocateObject
 NSCopyObject
 NSDeallocateObject
 NSDecrementExtraRefCountWasZero
 NSExtraRefCount
 NSIncrementExtraRefCount
 NSShouldRetainWithZone


=== Objective-C Runtime ===
    
 NSClassFromString
 NSGetSizeAndAlignment
 NSLog
 NSLogv
 NSSelectorFromString
 NSStringFromClass
 NSStringFromSelector


=== Path Utilities ===
    
 NSFullUserName
 NSHomeDirectory
 NSHomeDirectoryForUser
 NSOpenStepRootDirectory
 NSSearchPathForDirectoriesInDomains
 NSTemporaryDirectory
 NSUserName


=== Points ===
    
 NSEqualPoints
 NSMakePoint
 NSPointFromString
 NSStringFromPoint


=== Ranges ===
    
 NSEqualRanges
 NSIntersectionRange
 NSLocationInRange
 NSMakeRange
 NSMaxRange
 NSRangeFromString
 NSStringFromRange
 NSUnionRange


=== Rects ===
    
 NSContainsRect
 NSDivideRect
 NSEqualRects
 NSIsEmptyRect
 NSHeight
 NSInsetRect
 NSIntegralRect
 NSIntersectionRect
 NSIntersectsRect
 NSMakeRect
 NSMaxX
 NSMaxY
 NSMidX
 NSMidY
 NSMinX
 NSMinY
 NSMouseInRect
 NSOffsetRect
 NSPointInRect
 NSRectFromString
 NSStringFromRect
 NSUnionRect
 NSWidth


=== Sizes ===
    
 NSEqualSizes
 NSMakeSize
 NSSizeFromString
 NSStringFromSize


=== Uncaught Exception Handlers ===
    
 NSGetUncaughtExceptionHandler
 NSSetUncaughtExceptionHandler


=== Zones ===
    
 NSAllocateMemoryPages
 NSCopyMemoryPages
 NSCreateZone
 NSDeallocateMemoryPages
 NSDefaultMallocZone
 NSLogPageSize
 NSPageSize
 NSRealMemoryAvailable
 NSRecycleZone
 NSRoundDownToMultipleOfPageSize
 NSRoundUpToMultipleOfPageSize
 NSSetZoneName
 NSZoneCalloc
 NSZoneFree
 NSZoneFromPointer
 NSZoneMalloc
 NSZoneName
 NSZoneRealloc

== FoundationKit structs ==
(**Note:** These are C structs, not classes.)
    
 NSPoint
 NSRange
 NSRect
 NSSize

