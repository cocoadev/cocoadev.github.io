---
layout: page
title: VersioningWithKeyedArchiver
---


I am trying to get versioning going now on a Application already distributed to many people. My App saves 2 arrays to a file which is then retrieved on startup. However now trying to add versioning is causing problems as I cannot get any version information to saved with the archvied information saved with General/NSKeyedArchiver. I thought a while ago I'd read that for some reason you can't save version information with General/NSKeyedArchiver and I was wondering if thats true? I've been trying using versioning with the same code that most people probably use:
    
if(self == General/[MyClass class]) {
[self setVersion:2];
}

in the -(id)init method.

I thought I'd found a solution with the method -(void)setNilValueForKey:(General/NSString *)key in that I assumed that if when the General/NSKeyedUnarchiver couldn't find a value for a key in an instance and finds a nil value (object does not exist because it hasn't been encoded ) it would call a method that I could override and fill in a default value for a variable in an object, however it appears that this particular method setNilValueForKey: deals only with numeric values in certain situations. 

So my question basically is, is there a way to encode class version information with General/KeyedArchiving or is there some method I could override in each instance class that is called with the General/NSKeyedUnarchiver can't find a value for a key in each object as it's being unarchived? Or is there some other solution altogether? Honestly the most surprising thing i've found is that my program hasn't crashed when it's unarchiving information and retrieving nil values for keys that were never encoded.
----
Well, starting now, you could encode the class version in your     encodeWithCoder: method and check it later in your     initWithCoder:. Or you could just check and see what values are     nil and set them later. Finally, I've never seen anything that says versioning is not supported by General/NSKeyedArchiver. This is probably because, versioning is classwide, so the above code should be in your     +(void)initialize method. And just for curiosity's sake, the reason you have to check whether or not your class is really your class (    self == [M<nowiki/>yClass class] is to make sure that subclasses don't actually have *their* versions updated too. --General/JediKnil

*
The comment about versioning not being supported by General/NSKeyedArchiver may be in reference to Apple's documentation on General/NSCoder's versionForClassName: which states, "The version number applies to General/NSArchiver/General/NSUnarchiver, but not to General/NSKeyedArchiver/General/NSKeyedUnarchiver. A keyed archiver does not encode class version numbers." --Christopher Drum
*
----
I also was just thinking about that and tonight discovered that the following appears to work:

    
-(id)initWithCoder:(General/NSCoder *)coder
{
if([coder decodeObjectForKey:ASN_KEY] == NULL) {
[self setASN:@"Object default value"];
} else {
[self setASN:[coder decodeObjectForKey:ASN_KEY]];
}

If this will work anywhere then I may just use this instead of trying to do any versioning because instead of keeping track of the version you just need to keep track of the original set of vars in an object class and check for NULL values on additional vars added later on.

*You might want to save the value of     ASN_KEY to an variable to avoid calling     decodeObjectForKey: twice. Also, you should really test against     nil, even though it is equivalent to     NULL (better style --     nil is considered an object, whereas     NULL is a pointer to nowhere). --General/JediKnil*
----

In a popular production application, I use an archived General/NSDictionary. One of its keys is @"Version" which contains the *application version* last used to archive it. If a future version expecting extra data comes along and sees no data, it fills in the default or handles it however is appropriate. If a previous version opens a future version, it can simply ignore the data for the higher version's keys. The bottom line is, however, that this is a safe and simple way of storing this data which allows for incremental format updating. What I mean by that is that if 1.5 encounters a 1.0 file, it can step through the updateIfNecessary (or whatever) routine saying "if it's 1.0 format and I'm a higher version, do the magic to update it to 1.1" then "if it's 1.1, do the magic to update it to 1.2" etc. Each time you change the format, you can insert another if statement. If it's all executed in order, a user who waited way too long to update will be just fine as all the format changes will be applied incrementally. One caveat: If you change the *format* of the data stored in a particular key or rename a key, the file will *not* be backward-compatible. Easy, reliable, reasonably backward-compatible in many cases.
----
Thanks for all the help I think I will use the coding style
    
-(id)initWithCoder:(General/NSCoder *)coder
{
if([coder decodeObjectForKey:ASN_KEY] == nil) {
[self setASN:@"Object default value"];
} else {
[self setASN:[coder decodeObjectForKey:ASN_KEY]];
}

and try to design a good versioning system for the archiving just like in the post above this one. And my keys are stored as Global variables just to make things easier I just didn't show it in the code example. Thanks for all the help!

*I think what he meant by saving the value of ASN_KEY was to call     id theValue = [coder decodeObjectForKey:ASN_KEY] *once* and work with "theValue", rather than call decodeObjectForKey multiple times. For this particular situation (loading a file into memory), this really isn't going to impact performance too greatly, but it's all the little things that add up - this is bad practice that should be curbed early. Telling it to do a more expensive operation several times (such as decoding an object or even reading a user preferences value) when you only need to do it once is expensive and will slow down your application that much more.*
