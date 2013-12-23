---
layout: page
title: UsingStaticInMethods
---



Lately, I've noticed that many of my coding implementations strive to minimize the number of instance variables, because that I somehow get the notion that many of those are wrong, just like global variables are wrong. Or, at least a bit like that; you don't want ivars that don't have anything to do with the _data_ state of your object.

So, in many occations I've tried using static variables, with some influence (I think) from practices I learnt in a course I took: "declare variables near the place you use them", and "don't scatter the implementation".

Some examples of usage:

See the page General/AvoidAwakeFromNib, where a fix has been proposed. I would like to store the BOOL didAwakeFromNib like so:
    
- (void)awakeFromNib {
    static BOOL didAwakeFromNib;
    // ...
}


This is an ugly one, but here I save the last set value of each pref, and send away a message if the specific pref actually changed
    
// My controller recieves this upon prefs change. Passes on to the table.
-(void)prefsChanged:(id)ignored {
	General/NSUserDefaults *defaults = General/[NSUserDefaults standardUserDefaults];
	
	// ...
	BOOL showDate = [defaults boolForKey:General/USShowDate];
	static BOOL savedDate = -1;
	if(savedDate != showDate) {
		savedDate = showDate;
		[table setShowColumn:showDate withIdentifier:@"date"];
	}
}


From a simple screen saver (yeah I know, this is inefficient. Perhaps I should store the widths)
    
- (void)drawRect:(General/NSRect)rect
{
	// ...
	
	static int offset = 0;
	[text drawAtPoint:General/NSMakePoint(offset - [text size].width, 10)];
	offset = (offset + 3) % (int)([self bounds].size.width + [text size].width); 
}


Finally, is there a reason for this coding practice, seen in General/JuggleWithGlobalVariables
    
static General/DirectorySupport *sharedInstance = nil;
+ (General/DirectorySupport *)sharedInstance
{
    return sharedInstance ? sharedInstance : (sharedInstance = General/self alloc] init]);
}

when I would like the static to be declared inside the method, just because it's cleaner.  (This way, the pointer wouldn't even be alloced if the sharedInstance was never requested. But that's a silly point)

Comments are welcome.
[[EnglaBenny

----
This is a fairly good idea and has some code friendliness to it, but IIRC statics declared in methods (like functions) are shared across all instances of the object. So statics can really only be used in rare cases (or singleton objects). ~ General/TimothyHatcher
----
You're right, General/TimothyHatcher - static variables are shared across all instances of the object (if I recall, a static variable in General/PlainOldC is one which preserves its value between calls to a function - and when you get down to it, General/ObjC methods are just General/PlainOldC functions with extra bells and whistles).

I've found them useful in class methods, like +[load], which might be called many times but which should only be executed once (eg. when a singleton class's code is loaded), thusly:
    
+(void)load{
	static BOOL loaded=NO;
	if(!loaded){
		loaded=YES;

		// do once-only initialisation here

		}
	}

--General/ToM
----
I *strongly* recommend against using     static in a regular instance method. It's fine in a class method, but avoid it like the plague in an instance method. As noted, you can only use it if you're sure that the class is a singleton (or if you've worked out a complicated protocol for sharing the variable between them), and in that case there is no difference between putting it in the method or in your instance variables. In particular, code should always be designed with flexibility in mind. If one day you decide to make your class not be a singleton, and you forget about this one static variable, it's likely to come back and bite you.

If you deliberately want to communicate between instances, treat the class as an object and wrap the variables in class methods that manipulate it.
