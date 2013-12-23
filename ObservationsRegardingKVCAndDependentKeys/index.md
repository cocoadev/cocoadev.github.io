---
layout: page
title: ObservationsRegardingKVCAndDependentKeys
---


So, I've been trying to replace the notification I've implemented in my code with proper KVC methods. I have been frustrated by +setKeys:triggerChangeNotificationsForDependentKey:, so I copied Apple's test app and ran it with debugger marks throughout. Despite my best efforts, the program simply never entered the fullName method UNTIL I changed the [self addObserver...] code to observe the fullName key. I had initially thought that the fullName method would be invoked whenever firstName or lastName was changed. This is how I interpreted the Apple docs, and it seemed sensible that the program would trigger the fullName method if first or last name changed, thus allowing the program to generate an updated full name. This is NOT the case. It seems that you need to include something that's waiting for an update of fullName. I guess, from the point of view of not doing things you don't need to do, this makes sense. The program doesn't bother to generate a fullName unless you tell it you need one. I had (erroneously) included code that pushed information to other keys and expected that this would occur automatically. Anyhow, if this is obvious to you, read no further. If it isn't, as it wasn't to me, then this seems to be how to implement KVC by hand. Most of my program uses keys that were part of my xcdatamodel, but one value was strictly calculated in a separate object, so I needed to include this sort of code as well. If I manage to get it working in the real program (BillingProject, in the open source section here), a more real world example will be available. -Dan
    
@implementation KVOTest

+ (void)initialize
{ 
	[self setKeys:[NSArray arrayWithObjects:@"firstName",@"lastName",nil] 
	triggerChangeNotificationsForDependentKey:@"fullName"];
}

-(void)awakeFromNib
{
	// This is where you need to include the "fullName" key so that the fullName method gets invoked. Choosing another key here
	// (for example, firstName) will result in fullName NOT being invoked in this program.
	[self addObserver:self forKeyPath:@"fullName" options:(NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld) context:nil];
}

- (void)observeValueForKeyPath:(NSString *)myKeyPath ofObject:(id)myObject change:(NSDictionary *)myChange context:(void *)myContext
{
	NSLog (@"Observing Value for Key Path: %@; Object: %@; Change: %@....", myKeyPath, myObject, myChange);
	NSLog (@"Done observing.\n");
}

-(IBAction)myPushMe:(id)sender
{
	// An IB button invokes this method so I can follow the steps in the GDB. Changing the key to "firstName" also works, since it's registered
	// in the initialize method as well.
	[self setValue:@"someNewValue" forKey:@"lastName"];
}

- (NSString *)fullName { 
	return [NSString stringWithFormat:@"%@ %@", 
		[self firstName],[self lastName]]; 
} 
- (NSString *)firstName { 
	return firstName; 
} 
- (void)setFirstName:(NSString *)newFirstName { 
	[newFirstName retain]; 
	[firstName autorelease]; 
	firstName=newFirstName; 
} 
- (NSString *)lastName { 
	return lastName; 
} 
- (void)setLastName:(NSString *)newLastName { 
	[newLastName retain]; 
	[lastName autorelease];
	lastName=newLastName;
}
@end

Now, the next move is to have a set of keys that are observed for changes. When a change occurs, the program should send a message to a model object to update a dependent value. (the myStartTime and myEndTime variables are bound, via IBPalate bindings, to datePickers. They work fine in the version of the program that triggers total bill calculations through generic notifications. However, no matter what I've tried, they fail to invoke any sort of KVC-mediated bill calculation. For example:
    
@implementation MyBillableController

+(void) initialize
{
	[super initialize];
	[self setKeys:[NSArray arrayWithObjects:@"selection.myStartTime", @"selection.myEndTime", nil]
	triggerChangeNotificationsForDependentKey:@"myEventBill"];

// The dependent key is actually a pair of methods (getter/setter) in myBillableEventModel. I have included an
// IB outlet to this object, but if I insert NSLog lines in the awakeFromNib method of this controller
// object, the runtime tells me that it's not KVC compliant for any of the following keys:
// @"myEventBill", @"selection.myEventBill", or @"myBillableEventModelOutlet.myEventBill".
// Worse yet, if I write a pair of myEventBill methods for this controller then have them send a
// myEventBill message to myBillableEventModelOutlet, that never occurs, even with the following code
// in awakeFromNib....
}

-(void)awakeFromNib
{
// myEventTotalOutlet is an IBOutlet to an NSTextField that should contain the calculated bill float.

	[myEventTotalOutlet bind:@"value"
					toObject:self
				 withKeyPath:@"myEventBill"
					 options:[NSDictionary dictionaryWithObjects:[NSArray arrayWithObjects:[NSNumber numberWithBool:YES], nil]
					 forKeys:[NSArray arrayWithObjects:NSContinuouslyUpdatesValueBindingOption, nil]]];

	[self addObserver:self forKeyPath:@"myEventBill" options:(NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld) context:nil];
// I've tried replacing one or both selfs with myEventTotalOutlet, myBillableEventModelOutlet, and anything else I can think if without
// ever managing to invoke myEventBill in this controller or in the model object.

// Continues with awakeFromNib.

So, anyone know what I've done incorrectly? It seems that KVC should be much easier than I'm finding it, so it's probably an obvious error I've made here. I just can't figure it out. Thanks again! -Dan

----

In the firstName/lastName example you give, the fullName method will not automatically be invoked with a change to {first,last}Name unless something is bound to it using bindings.  Or, as you discovered, some object is observing that key.

-sbooth

----

how about -willChangeValueForKey:/-didChangeValueForKey: in your set methods ?

----
First responder: Yes, it seems that something needs to "care" for the computer to invoke the dependent key. However, in the second example, I thought I had properly bound the NSTextField to the myEventBill key. Nevertheless, the program still fails to invoke the myEventBill getter, even when I include the code in the controller object itself.
Second responder: The will change/did change methods aren't part of KVC compliance, acording to page 25 of the KVC coding manual from Apple. I got the impression that these methods were strictly NOT to be used if you rely on automatic Key-Value binding, which is the default setting. Is that wrong? Are they not already inherited from NSObject?

(Taken from the manual for NSKeyValueObservingProtocol):

automaticallyNotifiesObserversForKey:
Returns a Boolean value that indicates whether the receiver supports automatic key-value observation for the given key.

+ (BOOL)automaticallyNotifiesObserversForKey:(NSString *)key

Return Value
YES if the key-value observing machinery should automatically invoke willChangeValueForKey:/didChangeValueForKey: and willChange:valuesAtIndexes:forKey:/didChange:valuesAtIndexes:forKey: whenever instances of the class receive key-value coding messages for the key, or mutating key-value-coding-compliant methods for the key are invoked; otherwise NO.

Discussion
The default implementation returns YES.

-Dan

----

In the second example you give, you attempt to set dependent keys using a key path.  This behavior isn't supported for setKeys:triggerChangeNotificationsForDependentKey:- this method only supports single keys.  There is some information that could be useful on the Core Data FAQ page: http://developer.apple.com/documentation/Cocoa/Conceptual/CoreData/Articles/cdFAQ.html#//apple_ref/doc/uid/TP40001802-DontLinkElementID_26

Without seeing much of the code, it appears you are subclassing NSObjectController.  Is this really necessary?  An alternate approach would be to duplicate the myStartTime and myEndTime in your model object, and have the actions for the DatePickers (or whatever control you're using to choose the dates) update the appropriate field in the model.

-sbooth
----
sbooth-
Thanks for responding! I did subclass NSArrayController for this application so that I could better regulate the NSDatePickers which are bound to the xcdatamodel through IB palate settings. Specifically, I wanted to be able to disallow certain date choices. Nevertheless, the dependentKeys thing still isn't working for me. Basically, when a user changes a date or time, the computer should recalculate the total bill based on the new time interval. Even when I write matching getter and setter methods IN THE CONTROLLER OBJECT, they fail to be invoked. (Along the lines of your suggestion (I think), I had tried to use methods in the controller object to invoke methods in the model object, but I still couldn't get the dependentKeys method to work.) I just want to know how (or even if it's possible) to have the dependent keys listed above lead to the invocation of a particular getter method by using KVC. The code works as it is by using notificaitons (I look at TextDidEndEditing for NSTextFields (not shown) or at the validation method for the NSDatePickers, then post notifications that way), but since this specialized notification system exists in 10.4, I figured I try using it instead. I can post more code if it will make clearer what I've tried. Thanks again for any insight or suggestions!

Incidentally, how does a controller "know" which model object contains the correct key, anyway? Does the runtime look at all methods and divine the correct object from that? I assume the xcdatamodel has priority, so to speak, but unless accessor methods are supposed to be written into the controller, I don't see how the controller is linked to the model. -Dan
----
Hi Dan,

I've gotten a bit confused about your application's architecture- is this a Core Data app?  If it isn't, I'm not sure what good the xcdatamodel will do you.

I threw together an extremely simple demo that I think contains the essence of what you're trying to do.  Please download http://sbooth.org/files/KVCTest.zip and let me know if that helps clear anything up.

-sbooth
----
sbooth-
Yes, it's a CoreData app. I was trying to learn something about bindings and about KVC, so I followed the Apple video at http://developer.apple.com/cocoa/coredatatutorial/index.html . This was wonderfully informative, but it left a few questions, such as how to establish KVO/KVB programmatically. I designed an xcdatamodel that contained all the important data I wanted to archive for a billing program so that I could track multiple clients (and their contact information) as well as multiple billable events (start & end time, expenses, notes, whether they've been paid) for each client. The program outputs a crude but usable bill which can be pasted into any text program. There were to be a few calculated variable as well (such as an event's total value: time * rate + expenses), so I created a separate model object that contained the formulas to calculate these data. I simply want to use the billable event controller (as opposed to the client controller) to glue the calculated value from the model to the UI in an NSTextField. Anyway, I'll download the sample you've posted and see if I can answer some questions. The code I've written is available at http://www.ascendiac.com/macosx.html (it currently uses notifications to force these calculations to occur), but I'd like to employ KVC instead, since it's sort of how OS X 10.4 is apparently "supposed" to work. Thanks for your ongoing support!
---

OK, so your example is exactly how I expected this should work. The only thing that jumps out at me as being different from what I've tried so far is that you don't have an independent controller object as an intermediary. I can't quite figure out what I need to do with my code because I have the xcdatamodel which is readily accessed via an NSArrayController using KVC, and a separate data model which I was using for these other calculations. Can I simply use keys such as @"selection.myStartTime" in the billableEventModel even though the key refers to the xcdatamodel? Should I #import "MyDocument.xcdatamodel" in this object? (*Nope, that doesn't work.*) -Dan
----
So, for anyone still reading this thread, let me ask a very direct question: Is it even possible to use +setKeys:triggerChangeNotificationsForDependentKey: for an NSArrayController and an xcdatamodel object? I have spent hours with GDB trying to understand the construct here. The key can't be accessed directly in the controller, but rather by accessing the controller's selection (or arrangedObjects or selectedObjects) method, then sending a valueForKey: message to THAT object. (I have also had no success using @"selection.myKey" as the key pattern in this method.) This means that, in an +initialize method, sending the +setKeys:triggerChangeNotificationsForDependentKey message does NOT get received by the object that can respond to valueForKey. Since the setKeys:triggerChangeNotificationsForDependentKey method does not seem to accept paths, it looks like this construct will not work in an xcdatamodel-dependent design with a controller layer and a separate model used to perform on-the-fly calculations dependent on changes in key values stored in the xcdatamodel. If there IS a way to arrange for the xcdatamodel's keys to trigger the setKeys:triggerChangeNotificationsForDependentKey, please show me how. Every example I can find uses hand-coded getter and setter methods in the model layer. I suppose I could re-construct skeleton getters and setters which simply access the xcdatamodel object via key-value requests (say, by writing methods for each key akin to return myControllerOutlet selection] valueForKey:@"myKey" and ...setValue:forKey:) but that would appear to defeat the entire purpose of the xcdatamodel interface design.
Any thoughts? -Dan

----

Dan, you seem to be using a bunch of terminology incorrectly above ("xcdatamodel interface design") that makes it hard to figure out what you're really trying to accomplish.  I suspect you're making things harder for yourself than they really need to be, but I can't be sure since.  You're correct though that a method which uses the term *key* in its name instead of *key path* will not work with key paths; this is by design.

Since this is really a [[MailingListMode discussion, I strongly suggest you post your question to Apple's cocoa-dev mailing list.  A large number of experienced Cocoa developers are on the list (including many people working on Cocoa and related technologies at Apple), and you'll be much more likely to get a good answer -- or a good series of questions to help figure out what you're trying to achieve -- through that route than you are by posting on the wiki.

