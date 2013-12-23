---
layout: page
title: NSCoder
---

Apple's NSCoder docs: http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSCoder_Class/Reference/NSCoder.html

Apple's Docs on the concept of Archiving: http://developer.apple.com/documentation/Cocoa/Conceptual/Archiving/
----

NSCoder is an abstractClass which represents a stream of data. They are used in Archiving and Unarchiving.

you can read data from an NSCoder or write data to an NSCoder.

NSCoder objects are usually used in a method that is being implemented so that the class conforms to the <NSCoding> protocol.
You generally don't need to intialise NSCoder objects - you will be given them as an argument to an <NSCoding> method.
When a class implements the <NSCoding> protocol it can initialise itself from a coder or write itself out to a coder.

----

Some of NSCoder's encoding methods: (often used in <NSCoding's> encodeWithCoder:)

    
-(void)encodeObject:(id)anObject

causes anObject's encodeWithCoder method to be called. (i.e. it gets the object to encode itself into the data stream.)
    
-(void)encodeValueOfObjCType:(const char *)typeString at:(const void *)address


like encodeObject: but used when you want to encode a non-object instance variable.



There are also decoding methods: (often used in <NSCoding's> initWithCoder:)

    
-(id)decodeObject


    
-(void)decodeValueOfObjCType:(const char *)typeString at:(void *)data



The Class must encode objects and structs in exactly the same order as it decodes them (and vice-versa) - otherwise your class will not properly (un/)archive itself.

----

Here is an example - here are the two <NSCoding> methods for some fictional class (fooClass) that has four instance variables: an NSString, an NSDictionary, a BOOL and a float.  The class also has accessor methods for setting the values of these variables.

When an instantiated fooClass object is sent the encodeWithCoder: message it will encode its instance variables (objects and structs) into the NSCoder.

When sent the message initWithCoder: the Class will initialise itself by decoding the variables out of the NSCoder.

*n.b.* **if the class inherits from a Class that conforms to <NSCoding> (NSObject does not conform) then you should include the bold lines.  Else include the italic line.**
    
//	<NSCoding> protocol methods

-(void)encodeWithCoder:(NSCoder*)coder
{
	**[super encodeWithCoder:coder];**
    [coder encodeObject: theNSStringInstanceVariable];
    [coder encodeObject: theNSDictionaryInstanceVariable];
    [coder encodeValueOfObjCType:@encode(BOOL) at:&theBooleanInstanceVariable];
    [coder encodeValueOfObjCType:@encode(float) at:&theFloatInstanceVariable];
}

-(id)initWithCoder:(NSCoder*)coder
{
  **  if (self=[super initWithCoder:coder]) {**
    *if (self=[super init]) {*
        [self setTheNSStringInstanceVariable:[coder decodeObject]];
        [self setTheNSDictionaryInstanceVariable:[coder decodeObject]];
        [coder decodeValueOfObjCType:@encode(BOOL) at:& theBooleanInstanceVariable];
	[coder decodeValueOfObjCType:@encode(float) at:& theFloatInstanceVariable];
    }
    return self;
}



----
Over time you classes may change. The correct way to deal with this is with class versioning.  If you want them to be able to gracefully initFromCoder: from  an archive of an older version of your class you may want to look at VersioningUsingCoder

----

My first go at being useful here! DiggoryLaycock.  p.s. if it's poorly written or inaccurate make it better!

p.p.s. Hillegass's book covers this subject much more elegantly than I can! (see "Cocoa Programming for Mac OS X" in CocoaBooks)

----

Hey, do you need to retain the value returned by -decodeObject (Non keyed)? This is how I have it currently:

    - (id)initWithCoder:(NSCoder *)coder
{
    if ((self = [super init]) != nil)
	{
		nc = [NSNotificationCenter defaultCenter];
		
		if ([coder allowsKeyedCoding])
		{
			subView = coder decodeObjectForKey:[[TRSubviewKey] retain];
		}
		else
		{
			subView = [coder decodeObject];
		}
    }
    return self;
}

- (void)encodeWithCoder:(NSCoder *)coder
{
	if ([coder allowsKeyedCoding])
	{
		[coder encodeObject:subView forKey:TRSubviewKey];
	}
	else
	{
		[coder encodeObject:subView];
	}
	
}

----

From Apple's docs:

*
The implementation for the concrete subclass NSUnarchiver returns an object that is retained by the unarchiver and is released when the unarchiver is deallocated. Therefore, you must retain the returned object before releasing the unarchiver. NSKeyedUnarchiver�s implementation, however, returns an autoreleased object, so its life is the same as the current autorelease pool instead of the keyed unarchiver.
*

----

NB: 1- In case the super-class doesn't implement the NSCoding protocol, the super-class still requires to initialize its instance variables, hence why you need to do if(self = [super init]) in your own -initWithCoder:.
2- The init message to send to super is usually the super-class designated initializer. In case of NSObject it is -init.
 Pour participer   maintiennent numéro, vous aurez  peuvent avoir  compte  opérateur d'identification  (code RIO ) [http://obtenir-rio.info numéro rio]. Vous obtiendrez  pour  par  contacter   du serveur ou du service à la clientèle  satisfaction client  du  vieille fournisseur  [http://obtenir-rio.info/rio-bouygues numero rio bouygues] . Vous ne  mai   obtenir  un SMS avec votre . Avec votre  [http://obtenir-rio.info/rio-orange code rio orange], alors  vous êtes capable d'  vers le  offre de  de son   en orange orange .

