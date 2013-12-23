---
layout: page
title: BindingQuestions
---



Q: Is it possible to have an array controller for an array of the currently selected item in a table view controlled by another array controller?

A: yes, you can bind the contentArray of the *nested* array controller to the master array controller.

see example at http://cocoadevcentral.com/articles/000080.php

----

Q: when using an NSArrayController, what is the best way to populate the NSMutableDictionary items instantiated on add:?

A: create a dummy class like this:
    
@interface MyDefaultTableViewItem : NSObject { }
@end

@implementation MyDefaultTableViewItem
- (id)init
{
   [self release];
   return [[NSMutableDictionary dictionaryWithObjectsAndKeys:
      someObject,  someKey,
      otherObject, otherKey,
      nil] retain];
}
@end

Then in the attributes pane of the NSArrayController, set Object Class Name to MyDefaultTableViewItem.

----

Q: how are bindings actually set up in code?

A: there is an Application Kit Protocol Description for NSKeyValueBindingCreation -- it answers some questions. Also, check out BindingsInspector.

----

Q: is there any documentation on which KeyValueCoding methods one should implement? e.g. if I bind my NSArrayController's contentArray to File's Owner, using 'Actions' as model key, it will invoke �valueForKey:@"Actions"� to retrieve the array, but �setActions:anArray� to set it.

A: The Key Value Coding documentation describes the accessor search paths (check out "Accessor Search Implementation Details" at: http://developer.apple.com/documentation/Cocoa/Conceptual/KeyValueCoding/index.html )

�valueForKey:@"Actions"� will in turn invoke [self actions], [self isActions], [self getActions], or access the actions or _actions instance variables.

*This is the problem, if the key is Titlecase, it will invoke [self Actions] or access Actions as instance variable, so it is important that the key is named @"actions"*

Conversely, �setActions:anArray� will have been called from �setValue:anArray forKey:@"Actions"� ( or �takeStoredValue:anArray forKey:@"Actions"� )

----

Q: even though I have set all the placeholders to '0' for my NSPopupButton, it displays en empty string (and is disabled) when there is no value for this view. Why doesn't it show the first item (still disabled though)?

A: somehow it would seem that place holders are broke in IB. For example if I manually set up a binding to "selection.level" and provide a NSNoSelectionPlaceholder of 42, then my objects setLevel selector is invoked with '42' when there is no selection in the table view -- but I have failed to do the same in IB. I can provide a placeholder for a text field, but when this placeholder is used, the text field is disabled, even though I have not bound the 'enabled' key, so it would seem that IB tries to be intelligent about placeholders...

----

Q: is it possible to bind the enabled state of a text field to when a popup button has a given (selected) value? (probably I would have to write a value transform for this, but it would be rather hardcoded, since these have no configurable arguments).

A: Creating a value transformer was the first thing that occurred to me, too. Bear in mind that you probably only need to write one value transformer subclass, but you'll want to be able to instantiate that subclass with different values. In other words, your initialization method should take a parameter that is the value for which the transformer should return YES. Then you can just create and register as many instances of your transformer as you need, each with different values and names.

----

Q: if I have a table view controlled by an array controller, how do I know if individual items are changed? i.e. it will only setValue: on my model when the actual array is changed (adding or removing items), but not if the item is changed (i.e. setting an object-key pair for the item in question).

1/2 A: I'm not sure what you mean exactly, but if you add the following method to your model implementation, you can observe when in th MVC scheme your individual items are observed:

    

- (void)addObserver:(NSObject *)anObserver forKeyPath:(NSString *)keyPath 
	options:(NSKeyValueObservingOptions)options context:(void *)context
{
 	NSLog(@"MyModelObject addObserver:%@ forKeyPath:\"%@\" options:context:", 
		anObserver, keyPath);
	
	[super addObserver:anObserver forKeyPath:keyPath options:options context:context];
}



This gives you output like the following in your run log:

<code>

2004-03-08 09:36:16.291 MyApplication[1656] MyModelObject addObserver:<NSTableBinder: 0x366110>{object: <MyTableView: 0x351560>, bindings: content=arrangedObjects, selectionIndexes=selectionIndexes, sortDescriptors=sortDescriptors} forKeyPath:"title" options:(NSKeyValueObservingOptions)options context:(void *)context

</code>

You see that there are private objects dealing with observing your model data changes. To bad.


----

Q: Is there a way to implement a dialog similar to Panther mail's account preferences using the new bindings? Is there a way to tell if the selected item was edited and prompt for a save or cancel?

A: This is really two questions, with the first one being extremely abstract -- it'd say yes to the first and no to the second. If you want a more detailed answer, please provide a more detailed question, thanks! :)

R: It's not too abstract if you look at how the Account Preferences is set up in Mail.app in Panther. There is a table of servers and an add button, and a remove button. Clicking on a server in the table allows you to edit its fields in the text fields on the right hand side. However, the catch is that your changes aren't saved immediately. When you attempt to move away from the current server in anyway (close window, change server, change preference pane) it asks if you want to save what you just did. New servers are not completely added until you save them. I don't think there's a way to handle this with bindings because bindings save themselves immediately. It doesn't seem like there's an obvious way to subclass a controller to make it behave like this either. Or is there?

A#2: Ah, thanks for the clarification -- well, as far as I can tell, the values in the text fields could easily be saved to the contentArray of a potential array controller, however, this array would only be a copy of the array in the user defaults, and copied back when the user agrees to save the settings. The dialogs would have to be added from code, but by setting yourself up as a delegate to the table view, you can ask if the account should be saved (and abort the change in selection if this dialog is canceled), and thus should still be able to have the table view under control of an NSArrayController.

----

Q: Is there any way to use a binding to set the initial state of an interface element from User Preferences?  For instance, in a tab view of (say) New Messages, All Messages, Sent Messages, a user preference (controlled by a separate UI element) might specify which tab should open on program startup.  If I simply bind the tab to user preferences, it records every change in state (presumably a waste of effort) and records the final state of the Tab (when I only wanted a special preference to record the desired state!

I'm pretty sure this can't be done right now, but it would have been nice if Apple had included a binding for 'initial value'.

----

Q: Can I use the binding feature of XCode and make the target run on 10.2?

A: No, bindings require new methods and classes, which are only present in Panther.

----

Q: I have an NSArrayController bound to an NSTableView and I have a button that deletes items from the tableview through the array controller. If one item is selected, I want the button's title to be 'Delete Entry' and if more than one item is selected, I want the button's title to be 'Delete Entries'. Is there a way to do this using the NSArrayController?

A: Yes.  In fact there are many cases where this issue (puralization) comes up, so I took a generalized approach:  Start by writing some support into your model objects first.  For each model object, add two class methods:
    
+ (NSString*) objectName;
+ (NSString*) objectNamePlural;

which return the singular and pluralized names of the object (e.g. "Question" and "Questions").  (In your case, you're calling them "Entries", but you could be even nicer and call them what they really are, if you follow this example).

Then, in your controller which manages the array of those objects, write a method which examines the array's current count and returns the appropriate string.  For example:
    
- (NSString *)questionObjectNameForCurrentCount
{
	return (([questions count]==1)
		? [PollQuestion objectName]
		: [PollQuestion objectNamePlural]);
}

Now you're ready to implement your binding.  For example, say you have a table that displays one or more "Questions".  Below the table, you want to display an NSTextField which indicates how many questions are in the table.   You want it to say "1 question" if there's only one, or "10 questions" if there are 10 of them.  To do this, bind the NSTextField's "Value With Pattern" as follows:
    
   displayPatternValue1 = arrangedObjects.@count [QuestionsController (...)]
      Display Pattern:  %{value1}@ %{value2}@
  displayPatternValue2 = questionObjectNameForCurrentCount [File's Owner (...)]
      Display Pattern:  %{value1}@ %{value2}@


-- Larry Gerndt (lgerndt@mac.com)

----

Q: What's the relation between the NSTableView's *content* binding and NSTableColumn's *value* binding?

A: Apparently, setting an NSTableColumn's *value* binding automatically sets the NSTableView's *content* and related bindings.

----

Q: Why do controller objects themselves have bindings? What would you bind them to? Other controllers? To what end?

A: You may bind e.g. an array controllers array to a key in another controller if e.g. you have a parent table view where selecting an item will change the contents of a child table view.

An example would be a Mail-like interface, where a NSArrayController is handling the mailbox list and there's one "contents" object per mailbox -- you can bind the first controller to the mailbox table view, bind a NSArrayController's content object binding to a key path  that contains the first controller's selection (such as object: the first NSArrayController, binding: selection, key path: messagesArray or the like), and then every time you change your current mailbox, the messages list will change to match its contents.

----

Q: I bound an NSTextField's value and a NSStepper's value to the File's Owner of my nib file (NSObject subclass). I used a custom id object as Model Key Path. I set inital values both for the NSTextField and the NSStepper but when i launch the app why do the field contains no value?

*Are you setting the bound object's value through a KVC-compliant accessor?*

A: Yes, that was the problem, i added an accessor to my custom id object so i can initialize it in awakeFromNib:

----

Q: Given this from a few questions up: "setting an NSTableColumn's *value* binding automatically sets the NSTableView's *content* and related bindings.", is it possible to bind each column to a different NSArrayController? If so, how would one do this? I'm having trouble with it.

----

I'm not sure if this is really CoreData or a Bindings question, apologies for posting twice...

How do I create a SearchPredicate binding for an NSSearchField when I want to search within CoreData objects linked by a to-many relationship? If I want to return only objects for whom the object linked by a to-one relationship called *fubar* has the searched *name* property, I can just do make the predicate:

fubar.name contains[c] $value

But if *fubar* is a to-many relationship (*fubars*), and I want a match for any Fubar's *name*, I thought this predicate should work:

fubars.name contains[c] $value

But this doesn't work! How should I do this?

----

Q. I have an NSSearchField bound to an NSArrayController through the filterPredicate controller.  Now, whenever you type anything in the search field, the tableview only shows the records that match the search field (this is what I want).  However, if the user closes the window in a "searched state" and reopens the window, I would like to have all fields of the tableview showing again and the search field to be empty.  How do I programmatically set everything back to a default state? 

----

Q: I have a NSSearchField in a CustomView which I place into the toolbar. The binding of that SearchField doesn�t work (maybe because I can�t place it in the toolbar in IB?) so I want to do it with code but I�m not sure how.
I do this: [searchField bind:@"predicate" toObject:myArrayController withKeyPath:@"???" options:???];
I tried a few things where the "???" are, but it didn�t work. Hope you guys can help me with this.

----

I can't get my NSTableView to update (even on reloadData) when I addObject to my NSMutableArray.
My main "LEDController" class has an outlet to an NSTableView and has an NSMutableArray member:

    
@interface LEDController
{
 ...
  NSTableView *timePointTable;
  NSMutableArray* timePoints;   // "timePoints" holds objects of type Timepoint, see code below
 ...
}
- (LEDController *)init
{
 timePoints = [[NSMutableArray alloc] init];
 [timePoints addObject: [[TimePoint alloc] initWithTime: 0 andData: @"This shows up."]]];
return self;
}

- (void)buttonPress
{
  [timePoints addObject: [[TimePoint alloc] initWithTime: 1 andData: @"This doesn't."]]];
  [timePointTable reloadData];
}

@end

@interface Timepoint
{
double time;
NSString *data;
}
...
@ends


I hook everything up using an NSObjectController hooked to an LEDController and then use an NSArrayController, and bind it appropriately.

When I initialize my LEDController and populate "timePoints", they show up in the NSTableView just fine.

If I [timePoints addObject:] anywhere else and even do [tableView reloadData], the changes are not reflected in the TableView, I NSLog the "timePoints" array, and it indeed holds new values...

Steven Palm seems to have had the same problem here:
http://www.cocoabuilder.com/archive/message/cocoa/2004/12/10/123507

I believe this is something simple ...my app does have some timers and other things going on, but this seems to be independent of all that.

----

*You should be using the NSArrayController's add: method. Adding an object directly to the content array goes unnoticed by the bindings system. Alternatively, you can add your object, then call the array controller's rearrangeObjects method, but this is bad practice in this situation. You may want to read up on Apple's general documentation on "Cocoa Bindings". Also, search google for "Mmalc bindings examples" - explanations are there, too.*

*You don't need to use cocoa bindings at all, in fact, if you want better understanding of the language i suggest you don't, there are a few table tutorials here that may help you.*

If you're using Bindings to display your table data, you should (except in special circumstances, which this is not) use your array controller's add, insert, and remove methods so that the bindings architecture 'knows' the content array has changed.  BUT ... if your content array is being manipulated by an outside source (a special circumstance), then you can always use the KeyValueObserving methods to manually notify the bindings architecture of the changes to the content array.

----

I made an outlet to the NSArrayController from LEDController and use its addObject.

----

How do I get NSArrayController to manage an additional column that reports what percentage of the total value of some other column the current row has in that column.  I want to do this without having the model calculate it...

  Name   Money   Percentage


  Bob      10         50%


  Jane      6          30% 


  Terry    4           20%

----

Well this is actually two separate problems.  The first is how to total a column of data using NSArrayController.  This can be obtained by using KeyValueCoding and specifying the key @sum.Money.

For more details on array operators, see: 

http://developer.apple.com/documentation/Cocoa/Conceptual/KeyValueCoding/Concepts/ArrayOperators.html#//apple_ref/doc/uid/20002176

----

Alternatively, you could get the array of values for a single column into one place by linking the value of a field to a column.  You should then be able to total this by writing a 'total' NSValueConvertor.

The second problem is how to use this total (or any other value) to modify a value displayed in a single row.  You could use another NSValueConverter in this case, but you would have to declare it with global scope, which seems excessive!

You may have to end up subclassing the NSArrayController to add this functionality.

You might like to look at the NSKeyValueBindingCreation InformalProtocol:
http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Protocols/NSKeyValueBindingCreation.html

----

Q: How do I handle NSFormatter validation errors for NSTextFields which are bound to an object? I'm already catching them through the NSControl delegate method *control:didFailToFormatString:errorDescription:*, which works great. However, on text fields which use bindings, my standard delegate is called, AND an NSError object is sent down the responder chain, until NSApplication catches it and shows an ugly alert box. Ideally, I would just like to continue to use my delegate method to handle validation issues.

I suppose I could subclass NSWindow and try to catch the NSError before it's shown, or subclass NSObjectController and override its validation scheme... I know I don't want to implement validation directly on my model objects, since it's already working on the view level, through NSFormatter. Is there any simpler way?

----
I blogged about this: http://initgraf.blogspot.com/2007/11/customizing-errors-generated-by.html

----
Q: How do I bind an NSTextField to an unsigned int?

Some explanation: I want to bind my NSTextField to the selectionIndex key of an NSArrayController. Apple suggests (http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaBindings/Concepts/Troubleshooting.html#//apple_ref/doc/uid/TP40002148-182420) that I "consider transforming the value using an NSValueTransformer, or in the specific case of NSTextField, NSFormatter."

For some reason, I can't wrap my head around what, exactly, I should do with an NSFormatter to have my NSTextField send an unsigned int... Likewise, I can't figure out how to program an NSValueTransformer to send out an unsigned int, since it seem to have to send out some kind of object and not a primitive datatype.

----

I'm having trouble with something I feel SHOULD be possible, but I can't figure out how to do it.
I would like to bind interface elements so that, for example, a simple checkbox controls whether an NSTextField is hidden or not.
Check the box, it unhides the NSTextField. Uncheck the box, it hides the field.
I figure this SHOULD be doable without any significant code, but I've never seen an example like it.
I can't even swear that it is a bindings related thing.
Thanks for any help.

----

One way you might do this with bindings (and no code): Add a NSUserDefaultsController to your nib file that includes the NSTextField. Using the Bindings pane of the Inspector, bind the NSTextField's "hidden" binding to the controller with a Model Key Path like "XYZMyFieldShown". In your preferences window, whose nib also has a NSUserDefaultsController, bind the "value" of the checkbox to the controller using the same key. That's all. NSUserDefaultsController knows how to sync with the app's standard defaults (plist-based preferences). To be proper about this, the "factory default" value (YES or NO) should be registered when the app launches, with -registerDefaults . See NSUserDefaultsController and mmalc's wonderful Cocoa Bindings Examples and Hints page:

http://homepage.mac.com/mmalc/CocoaExamples/controllers.html

BTW, I remember doing an tutorial that covered just this, but I can't find it. This is too bad, because NSUserDefaultsController is by far the easiest way to use bindings and is a good first step. -- PaulCollins

----

You've got two controls that you want to share a piece of data. It sounds like you're trying to figure out how to bind one to the other, and that's generally not how bindings are used. Instead, you should be binding both of them to a value that's stored in a controller or model class. PaulCollins's description above accomplishes that using the defaults system. But more generally, just declare a value (a boolean value, in this case) as part of your model or controller, as appropriate, and bind both the value of the checkbox and the hidden attribute of the text field to that value.

In a model-view-controller world, model classes are supposed to store data while view classes are supposed to display that data, but not maintain it. Bindings provide a way to automate the connection between view and model, but don't change the fact that data should be stored in the model and not in the views. Given that, it doesn't make much sense to bind one view to another, since views don't store useful data of their own. -CS

----

Q: I have an NSImageView subclass that allows files to be dropped onto it, complete with delegate methods for handling those dropped files. I have an NSArrayController hooked up to an NSTableView, et al. My question is this: I want to be able to change whether the NSImageView subclass is editable based on whether the NSTableView has a selection or not. I have tried various combinations of binding the image view's 'editable' binding to the array controller's selection, selectedIndex, etc. with different value transformers (NSIsNotNil, for instance), but none of them create the behaviour that I need. Am I missing something, or are the editable, enabled, etc. bindings for this control not the way to do this?

----
Q: I am having what I think this is a wonderfully common problem for beginners: binding across nibs. My main nib/xib file displays a list of objects each of which has some properties in NSTextFields that are displayed next to the name of my objects. For each object there is a button that loads a second nib/xib file that displays more properties for that object (a details window if you will). Now, I have an NSArrayController in the main nib/xib that should manage these NSTextFields across the nib/xib files (there is a Main Contoller that has the NSArrayContoller as an outlet). What I tried to do was subclass NSWindowContoller and make this new class the File's Owner for by second nib/xib file (the details window). Now, as part of the this subclass, I made a method -(void)bindFieldsTo:(NSArrayController *)myArrayController that hands the NSArrayContoller to the File's Owner of my second nib/xib, which then uses it to bind its NSTextFields to that controller with proper key paths. I call this method with the NSArrayContoller that is from the main nib/xib file after I load the second nib/xib file. I get no errors, but of course the binding does not work. Setting the owner to the Main Controller in the main nib/xib files when loading the second nib/xib does not help. I am assuming I have to link the nib/xib files by a common owner to bind across nibs/xibs, but I have so far failed to do that. Any suggestions?

A: Ok, this actually does work but you must make sure that you load the window of the NSWindowController before you set up the bindings.

