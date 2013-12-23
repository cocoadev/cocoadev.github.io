---
layout: page
title: NSDatePickerCellDelegateFunction
---



I need help getting an NSDatePickerCell to work properly with the delegate method: 
     - (void)datePickerCell:(NSDatePickerCell *)aDatePickerCell validateProposedDateValue:(NSDate **)proposedDateValue timeInterval:(NSTimeInterval *)proposedTimeInterval 

Specifically, I have two date pickers in my window: a start and end date/time. I'm using an NSArrayController and Cocoa Bindings to establish the user interface and associated data objects. I have an IBOutlet to each NSDatePicker from a custom delegate object. I can send a message like this to each outlet:
     [myStartDatePickerOutlet setDelegate: self]; 

However, when I implement the method for the validation, if I modify the maxDate, for example, the delegate method ends up calling itself in a recursive loop. Apparently, changing anything about the NSDatePicker object re-calls the validate method. So, I figured, I'd just modify the user-set date, but it's not obvious how to do this. The documentation states, "Discussion: When returning a new proposedDateValue, the NSDate instance should be autoreleased, and the proposedDateValue should not be released by the delegate." However, the method returns a void, so I don't know how to return anything else (I don't think I can just return something arbitrary, right?). When I try to change the NSDatePicker date directly in this method, I end up in the same recursive loop.

Any ideas? I just wanted to ensure that the start date precedes the end date, so I figured the easiest way to do this is to change the maxDate of the start and the minDate of the end to match the other's set date, but I get this ugly loop.

Thanks!
-Dan Wambold

----

The proposedDateValue is both an argument and a return value.  This is typed in OmniWeb, but here's the idea:

     
- (void)datePickerCell:(NSDatePickerCell *)aDatePickerCell validateProposedDateValue:(NSDate **)proposedDateValue timeInterval:(NSTimeInterval *)proposedTimeInterval 
{ 
    if (doNotLikeTheProposedDate) {
        *proposedDateValue = dateValueILikeBetter; 
    }
}

----
I thought I understood how to use the above advice, but the error logs prove I do not. How does one manipulate an NSDate **? It doesn't take messages, and I can't even do this:
    
	NSDate *aDate = [[NSDate alloc] initWithTimeIntervalSinceNow:(NSTimeInterval)0];
	*proposedDateValue = aDate;

Sorry to be so dense, but I can't figure out the format, even using the GDB....
-Dan
---
OK, I think I found my error. I hadn't explicitly declared "proposedDateValue" as an NSDate **. I had typed it as (id), but the system didn't like that very much. Now that I've given it a proper type, I can manipulate it a little better. That's probably all I needed to do. Live & Learn. Thanks!
-Dan

----
The number of asterisks is highly important.     id is equivalent (other than static checking) to     NSDate * or     NSObject * or pointer to some other class. But what you have here is a pointer to a pointer to a class. If you wanted to type it as     id, the declaration would be     id *.

On a separate note, your "I can't even do this" code leaks. You need to autorelease     aDate.
----
On the autorelease: Yes, I was being sloppy here but I had included the proper memory management (thanks to specific notes in Apple's docs) in my code. Sorry about the poor posting etiquette here. Regarding the type id *, I had no idea that id * was even a valid construct. I have since statically typed the NSDate ** and found success, but in the future, I will know that id * would be required. I've seen handles in C, but I couldn't figure out the specifics in Obj-C when including id-typed objects. Thanks again for helping this rookie! -W

----
I always assume that the code posted here is the code being used, it's too hard to do anything else. So, good for you on having the right code in your program. As far as     id * goes,     anything * is always a legal C type meaning pointer to     anything. You can happily declare a variable as     float ****************************'var;, you'll just have a tough time using it.
----
I usually cut and paste code, so the assumption is definitely reasonable. Since you're obviously on top of this stuff, let me ask this: I tried several ways to double-dereference the     id that was actually an     NSDate ** without luck. How would one programmatically access the object pointed to by a handle so that the object responded to messages? I tried all combinations of     *s and     &s and even tried convincing the system by adding a type cast     (NSObject) to the dereference without luck. I'm afraid my basic skills with C are suspect; I've learned all I know from a few old books and trial & error. I guess I only think I "get it" when it comes to handles. I should stick to my day job, but I'm stubborn. Much obliged for the ongoing contact! -W
----
"Double-dereferencing" anything of type     id would never work no matter the combination of     *s and     &s...     id is defined to be a pointer to an     objc_object struct. Dereferencing     id once would give you the struct, which would invariably give you problems if you tried to dereference that.

Besides that, there really is never any reason to need to dereference an object's pointer at all anyway. In Objective-C, objects are always referenced by pointers whether sending messages or passing parameters or anything. And unless you are using objects that you've defined yourself and you have really good reason (of which there are very few or none from your persepective), there's no need to reference an object's ivars directly. So, don't worry about how to correctly dereference     ids at all...

----
To answer the question (how to get at the object in a handle so you can manipulate it), just dereference the handle once. Like so:

    
id *idHandle = ...;
NSDate **dateHandle = ...;

[*idHandle someMessage];
[*dateHandle someMessage];

----
Well, there you go. I did run into errors about trying to reference     objc_objects, so that's why I couldn't get things to work. Since I had erroneously typed the message parameters as id, the runtime system and GDB both balked at the ** construct I was attempting to use. Thanks for the insight. The help you provide through Cocoadev is truly invaluable! It keeps programming fun. -W

