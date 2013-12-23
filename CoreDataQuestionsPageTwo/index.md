---
layout: page
title: CoreDataQuestionsPageTwo
---

Is it possible to perform a conditional sum from the Data Model Editor and Interface Builder? I.e., something in the vein of:

    
SELECT SUM(expense) FROM Transactions
WHERE category = "bank";


(Transactions is the Entity, expense and category are attributes)
I would then bind an NSTextField to this hypothetical attribute (fetched property?) in the bindings info panel and be done with it. Or should I dig into doing things programmatically? (I've been trying to avoid that, as I'm a rather inexperienced programmer). Thanks! Marco

----

Offhand, I'd say that you need to break that up into different steps.  For instance, you can get all the Transactions where category = "bank" using NSPredicate.  You could then use the @sum key value operator.  Example:

    
NSArray *bankTransactions = [allTransactions filteredArrayUsingPredicate:[NSPredicate predicateWithFormat:@"category equals[cd] %@", @"bank"]];
NSNumber *sum = [bankTransactions valueForKeyPath:@"@sum.expense"];
NSLog(@"Sum is %@", sum);


If you wanted to do bindings, you could set up a     -(NSNumber *)bankSum method and have it change using the following in a Transaction class implementation/category:

    
+ (void)initialize
{
    [self setKeys:[NSArray arrayWithObjects:@"expense", nil] triggerChangeNotificationsForDependentKey:@"bankSum"];
}


----

Would such a solution be implementable in the Data Model Designer, meaning without code?

----

Sooner or later, you're going to have to write code.  Core Data is actually an advanced technology; while it can be used to very easily create NibWare, creating real applications will require a significant understanding of Cocoa including KeyValueCoding, KeyValueObserving, and CocoaBindings.

----

Thanks for the answer. Time, I suppose, to dig into this Cocoa coding thing. Not looking forward to the inevitable feelings of despair. Thanks again!

----

For the record, here is how I solved it in Interface Builder:
* Drag an array controller from the Palette to the Class Window
* Rename the array controller by double clicking on its name in the Class Window, e.g., BankTransactions
* Open the attributes inspector for the array controller and select the radio button for Entity
* Check Automatically prepares content
* Type the name of the Entity (in my case, Transactions)
* Set the Predicate to category == "bank"
* Open the bindings inspector for the array controller and set the managedObjectContext to the AppDelegate if your application is not document based, to the File's Owner if your application is document based
* Bind the NSTextField to the BankTransactions ArrayController, Controller Key: arrangedObjects, Mobile Key Path: @sum.expense

Now, I am trying to sort some of the popups. I am trying to follow Apple's NSPersistentDocument tutorial, adapt it to non-document based application and generally make it work less CoreData-ish (who invented the three click process to insert your data in the table?).

----

Why is a predicate like : "name LIKE[cd] 'georges'" 10 times slower than "name BEGINSWITH[cd] 'georges' AND name ENDSWITH[cd] 'georges'" ????

