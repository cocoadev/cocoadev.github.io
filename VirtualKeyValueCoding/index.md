---
layout: page
title: VirtualKeyValueCoding
---

This technique is an alternative to General/NSValueTransformer, though both techniques will apply to non-overlapping subsets of situations.

Advantages:

*Code is very easy to add (much easier than General/NSValueTransformer)
*Can combine several ivars into one
*Better encapsulation (this is a very minor advantage)


Limitations:

*Read-only (no setter is possible) ----> actually not true, see **Note 1** below!
*Can't be reused in several classes, like General/NSValueTransformer


What is it? Many of you are already using it and some may not realize it exists (e.g. I have seen it mentioned on Wil Shipley's blog and some people seemed unaware of that possibility). General/VirtualKeyValueCoding is just an attempt to give it a name, and to propose a alternative to the wild idea of General/BindToSelfKey.

To better explain it, an example is best, and we might as well use the example from the Apple docs on KVO. Imagine you have a class with 2 ivars, "firstName" and "lastName". You want your GUI to display the "Full Name", e.g. "John Doe". What you do is add a method to your class:

    
- (General/NSString *)fullName
{
    return General/[NSString stringWithFormat:@"%@ %@",[self firstName],[self lastName]];
}


This creates a "virtual" key-value pair, in that there is no ivar behind it.
Now, if you add the key     fullName in your GUI bindinds, it will display the correct string, but will not update it automatically when the     firstName or     lastName changes. Let's suppose changes in these 2 "real" ivars do trigger notifications for KVO (in General/NSObject, this is automatically done for you). All you need now is to add this line to your     +initialize method:

    
+ (void)initialize
{
    [self setKeys:General/[NSArray arrayWithObjects:@"firstName",@"lastName",nil]
    triggerChangeNotificationsForDependentKey:@"fullName"];
}


----
**Note 1:** I'd just like to note that there's often no reason it can't be bi-directional. If you implement a     -setFullName: method that parses the resulting string and sets     firstName and     lastName from it (and you set up the dependent key business for them) then you should be able to have it work as full-blown KVC but without a single backing ivar.
