---
layout: page
title: BindingTableColumnToArrayOfStrings
---

I am working from the O'Reilly MacDevCenter article on Bindings.

I understand the example, in which the model objects have KVC-enabled keys, and an array of model objects is controlled by an NSArrayController instance with columns in a table view bound to the model keys, and the content array of the array controller specified
as the array of model objects. The attributes of the array controller specify the type of object in the array, and model keys are added
to specify the KVC-enabled ivars within the model objects. Well and good.

What I want to do is set up a single-column table view as an inspector for an array of NSString objects. But I have no model keys for the
NSString objects. The interface for NSString doesn't give me anything to grab. So I must prepare a custom model object with a single
ivar consisting of the string. It would have been much if I could have sneaked by without, because....

The array of strings that I want is the set of unique strings at a particular key in ANOTHER array of objects which, incidentally,
is not an array of strings, nor is it an array of objects whose only ivar is an NSString.

----

D'oh! I see that I just need an intermediate array of NSString objects that I build up with the unique strings from the original array of objects.
Then iterate through that array and add them to the array of custom objects that I want to bind to the table.

Sorry for doing my thinking "out loud". But if anyone knows a fancier way, I'd love to see it.

----

make a category on NSString that implements -stringValue (or whatever KVC method you need)?

*Doesn't work...I tried it once. --JediKnil*

*Oh,wait! It does work...as long as you use NSMutableString and add a method like this: (boy, I **am** an idiot...) --JediKnil*
    
- (NSString *)string
{
	return [self copy];
}


No doubt you actually mean     return self copy] autorelease];. *Oops.*

Ooookaaay. At first, I was seriously puzzled when this didn't work in Panther, since it worked fine on my new Tiger system. However, it turns out that before Tiger, adding an observer to a string changed its class. (This is actually the standard method for implementing [[KeyValueObserving). As soon as I overrided the     addObserver:forKeyPath:options:context: method to keep the string's class from changing, it worked perfectly. Unfortunately, I have no idea if there are any other consequences to this, but one consolation seems to be that Tiger does the same thing. Here's the fixed implementation file (you can change the category name if you want). --JediKnil
    
#ifndef MAC_OS_X_VERSION_10_4
  #define MAC_OS_X_VERSION_10_4 1040
#endif

/*!
 * NSString now supports KVO! Using the key @"string" will work for both
 * mutable and immutable strings, thanks to this simple category. Works
 * with Cocoa Bindings too!
 */
@implementation NSString (ComBelkadanUtils_NSStringKVO)
/*!
 * Returns an autoreleased copy of this string, suitable for KVC purposes.
 *
 * @functiongroup Accessing the string
 */
- (NSString *)string
{
	return self copy] autorelease];
}

#if MAC_OS_X_VERSION_MIN_REQUIRED < MAC_OS_X_VERSION_10_4

/*!
 * The normal KVO methods change the class of the observed object as soon as
 * an observer is added. Because [[NSString is toll-free bridged with CFString,
 * this causes <em>serious</em> problems on pre-10.4 systems (I don't really
 * know why). This makes sure that any string's class will stay the same even
 * after an observer has been added.
 *
 * @functiongroup Overriden KVO methods
 */
- (void)addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath options:(NSKeyValueObservingOptions)options context:(void *)context
{
	Class strClass = self->isa;
	[super addObserver:observer forKeyPath:keyPath options:options context:context];
	self->isa = strClass;
}

#endif

@end


----

The class is changed because you don't return NO for all keys in method <code>+(BOOL) automaticallyNotifiesObserversForKey:(NSString *)key</code> for the class. Cocoa inserts a class there that automatically calls <code>willChangeValueForKey:</code> and <code>didChangeValueForKey:</code> when invoking setters. Return NO and do this manually to prevent this class change. -Petteri
----
Hmm...I didn't remember that, but unfortunately that would require that I override NSMutableString<nowiki/>s     setString: method, while keeping the original implementation...therefore requiring MethodSwizzling...which may not work on a ClassCluster. While this is definitely an undocumented and somewhat kludgish way to avoid a bridging error, I haven't yet suffered any ill effects. In Tiger it works fine even without the overriden     addObserver:forKeyPath:options:context: (added #if). I would think that somehow     setString: has to already be taking care of the problem, since the program is working on Panther without errors. (The best answer may be "switch to another key", but     string makes sense and     value is already taken. I think     stringValue might even be taken, but I'm not sure). --JediKnil

