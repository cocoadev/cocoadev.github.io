---
layout: page
title: DetectChangeInNSTableViewDataWithBindings
---



In a couple test apps I've made to learn Cocoa,  I've run into a snag.    As the data in my NSMutableArray is changed, via the NSTableView+NSArrayController,  I need to detect this and update a calculation which uses data from the NSMutableArray.

In one app, I have an Update button, which triggers my calculations and UpdateUI, which I would like to get rid of & just keep the output updated as the data changes.

In the other, It's pure bindings, and I'm using @avg.distance to average the totals from the 'distance' column/iVar.  It updates when I add/delete an entry, but not when other iVars are edited.    I am guessing this is because 'distance' is a calculated value, based on other iVars in the table, and not one that is directly 'set'.


So, is there a common method for detecting changes in my NSArrayController / NSMutableArray?

Maybe triggerChangeNotificationsForDependentKey  or   addObserver/observeValueForKeyPath ??    I think there is a delegate of NSTableVIew that will tell me when something is edited.  But, I the NStv isn't instantiated in my code, it's controlled by the NSArrayController..    Can anyone point a newbie in the right direction?

----

For the pure bindings situation, *forget the table view* ... the view layer is the wrong place to be looking for an answer. You definitely want to (re)read the documentation for Key Value Observing (KVO). You'll want to research +setKeys:triggerChangeNotificationsForDependentKey:

----

Thanks for the response.   triggerChangeNotificationsForDependentKey did the job for the bindings app, for the most part (it detects changes in directly bound objects, but not in iVars of nested objects.. which is probably good enough).

Now, I will move on to the other app.   I will be trying NSNotifications to do this (detect any change in data, and call a method to recalculate+display.  I think the same could be used for the above issue of changes in a nested object.

----

Your approach to this problem clearly indicates your need to completely re-read the Cocoa Bindings, Key Value Coding, and Key Value Observing guides. You are bypassing the very reason bindings / KVO / KVC exist for. These technologies make your approach completely unnecessary (and, in all likelihood, error-prone). Read, read, read!

----

Thanks.  I went through the KVO docs, and registering observers for all the relevant iVars took care of it.  

----

What about detecting objects removed/added to the NSMutableArray?  The +setKeys:triggerChangeNotificationsForDependentKey: method seems to only observes changes in the arrays objects and not the array itself. Am I misunderstanding this? 

----

Same answer. You're completely overlooking the very helpful documentation.

----

ARRRRGGGHHHH!! ENOUGH with the "RTFM" answers. Obviously the docs aren't clear/concise enough to be understandable. Either provide some help, or keep quiet - we all know about the friggin' FM.
-*annoyed*

----

Hrmm. Well, I'm very new to Cocoa and Objective-C and the bindings stuff is a little confusing at present. A pointer would help instead of just saying RTM, since it's not obvious to some people...

----

OK, so I've been trying to learn bindings and KVO with NSTableViews as well. I have a similar situation, where I have to calculate two values (simultaneously) based on a few different pieces of data. I have currently implemented this using a notification that is sent whenever the user edits data in any of the relevant fields (2 date pickers, a text field bound to one controller, or a text field bound to the other controller). I think I see how I could re-write this using KVO (and     +setKeys:triggerChangeNotificationsForDependentKey:), but it seems like I'd simply be using the KVO system as a specialized notification system. Given the fact that I have chosen not to keep this calculated value in the data model (it seemed wasteful since it can easily be recalculated), and the display of these calculated values occurs in two different places, is there a benefit to using KVO for this? A main purpose for writing this program was to improve my Cocoa programming skills, so if there's a cogent reason to use KVO for this last step, I'll do it. If you're really bored (or motivated), you can download the app and the source and critique it for me and anyone else who might use it as a guide (show what to do or, in the worst case, what NOT to do....) Thanks! -Dan (source at: http://www.ascendiac.com/macosx for BillingProject).

----

KeyValueObserving **is** a specialized notification system!  It's a specialized notification system for *exactly this kind of situation.*  Just because you're not *storing* your calculated value in a model layer object doesn't mean the calculation itself shouldn't take place there.  What is shown by your view objects should just be a reflection of what's happening in your model objects.  When the user changes something in a view object, your model should be changed to reflect that.  Both of these situations are mediated by controller objects.  This is the fundamental theorem of the ModelViewController pattern.

It's simple:  Bind your views (through controllers) to your model objects, not just for your model objects' concrete properties, but also for their calculated properties.  In a     +initialize method, use     +setKeys:triggerChangeNotificationsForDependentKey: to tell KeyValueObserving which concrete or calculated properties of your model objects your calculated properties depend on.  Then whenever you make a change, you don't have to post any notifications yourself; the notifications will **automatically** ripple through the system and cause your interface to be updated.  In other words, for the most part you can focus on your model and your views, and not have to spend a good chunk of your time pushing data back and forth between them.

----
FWIW, I have utterly failed to implement any sort of KVB by coding, as evidenced by a careful inspection with GDB. I have implemented an     +initialize method, including a     +setKeys:triggerChangeNotificationsForDependentKey: message to self, with a setKey of @"myStartTime" and a dependent key of @"myEB". In the same object (to keep things simple), I implemented both a method called myEB, which returns a float, and a setMyEB, which accepts a float. No matter how I change the myStartTime in the UI, the myEB method simply does not get executed. I added observeValueForKeyPath:ofObject:change:context: and addObserver:forKeyPath:options:context:, but the only path that appears in the method is the one defined in addObserver. In other words, inspection of the observeValueForKeyPath:(NSString *)path... variable path does not differentiate between triggering keys. In no case, however, does the myEB method get accessed, meaning that the value won't update without me specifically calling it from some other method, so the bound UI element (an NSTextField) doesn't get updated at all. Any advice would be greatly appreciated.

Also, what's the deal with keyPaths? Do I need to specify somehow that a particular key exists in another object than the receiver? I am writing these methods in an NSArrayController object, which controls the xcdatamodel, but the method that calculates the value I am trying to bind exists in a hand-coded object that is separate from this controller. I don't know how to tell the controller, for example, that a key it freely accesses in the xcdatamodel should trigger a change notification for another object. Should the     +setKeys:triggerChangeNotificationsForDependentKey: be sent to the controller? The hand-coded object? Does the runtime system know where the keys are without being handed the object, or is part of the keyPath supposed to contain the IBOutlet name for the targeted object? Again, thank you for any insight. I'm just too dense to get these answers from the Apple Docs, I guess. -Dan

/********' Obsolete Fragment ***************
I am still failing to achieve results with KVC/KVO. Here's what I have and have done: 1. View built in IB with (a) two TableViews with controllers. The left TableView is a list of clients, the right is a list of events for a given client. Only one of each can be selected at any time. (b) two NSDatePickers for start and end times. (c) an NSTextField for each client setting a billing rate. (d) an NSTextField for each billable event for "expenses" (e) an NSTextField for each billable event showing a total bill for that event. This field should show the automatically recalculated total of (EndDate - StartDate) * rate + expenses. 2. An xcdatamodel I built to contain all the above data (as strings, dates, or floats). This model is connected to the view by IB-created bindings. 3. Relationships between the two controllers (called MyBillableController and MyClientController). 4. A separate class called MyBillableEventModel which handles calculations such as determining the total bill by iterating through all of a given client's billable events, selecting only the unpaid events (based on a checkbox "PAID"'s status), determining each event's length, summing them, and placing the total in an xcdatamodel's variable for total hours, and also determining the currently selected event's net bill, which is returned as a string.
Now, I took out all the notifications I had installed and wrote, as a part of the billable event controller, and initialize method:
    
+(void) initialize
{
	[self setKeys:[NSArray arrayWithObjects:@"myStartTime", @"myEndTime", @"myPaidFlag", @"myExpenseAmount", @"billableEvent.myClientRate", nil]
	triggerChangeNotificationsForDependentKey:@"myBillableEventModelOutlet.myEventBill"];
}

First question: Why doesn't this cause the MyBillableEventModel's myEventBill method to be called? I have both a myEventBill method which returns an NSString* and a setMyEventBill method which accepts an NSString* but does nothing since the user is prohibited (via an IB setting) from editing the NSTextField that corresponds to the event bill value. Now, I have named the NSTextField that should contain the event bill value "myEventTotalOutlet." I put this message in the above billable event controller:
    
-(void) awakeFromNib
{
	[myEventTotalOutlet bind:@"value"
          toObject:myBillableEventModelOutlet
          withKeyPath:@"myEventBill"
          options:nil];
...

Next question: What have I done wrong? The NSTextField is eternally empty. I believe that notifications are being posted (somehow, yesterday, I managed to trigger some NSLog message with various implementations of observeValueForKeyPath... methods, but I have no idea what I did since they didn't really accomplish what I had hoped they would. I have both the 92 page "Cocoa Bindings Programming Topics" manual plus the 44 page "Key-Value Coding Programming Guide" here, and I've read them both yesterday. At least one point that's still baffling to me is where this stuff should exist in my code. Are these controller methods? If so, how do I access the arbitrary object MyBillableEventModel since it isn't part of the xcdatamodel and therefore doesn't have the luxury of being a key in the controller's vocabulary? Can I use the path I tried above, in +initialize? Does Cocoa figure out the key from there, or must I do other things to ensure that the controller object can access the key in the model object? Why is the method still not being executed? Can I use as a triggering key the path above, "billableEvent.myClientRate" or must I implement this same method in the client controller as well? Would it be better to expose the binding and wire it in IB? Is my MVC model too poorly constructed to allow KVB? Thanks again for any insight you might have. -Dan
************ End Obsolete Fragment ********************/

