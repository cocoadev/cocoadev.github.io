---
layout: page
title: NSArrayController
---



NSArrayController allows you to control an array, providing methods for adding, inserting and removing items of a given object class to this array.

Setting up a NSArrayController in Interface Builder is a three-step procedure. Assuming you bind it to an array containing NSMutableString instances, this may be the procedure.

----
There are some very simple and clear examples available at http://homepage.mac.com/mmalc/CocoaExamples/controllers.html
----
An excellent short explanation of the rationale' for NSArrayController is provided at http://www.cocoabuilder.com/archive/message/cocoa/2008/1/13/196372
----
First you bind the NSArrayController to your NSMutableArray. The array is in this case mutable so that you may add or delete elements.

* Open IB:s Inspector and select the Bindings panel (&#8984;+4).
* Bind the controller's contentArray binding to your array object.

* Set Model Key Path to the name of your array identifier.
* Select Attributes panel in Inspector window (&#8984;+1) and set class type of array's elements in Object Class Name. This is per default NSMutableDictionary and set it to NSMutableString. This means that the array controller manages an array of strings. NSMutableDictionary can work as an "universal" object class since it maps -setValue:forKey: to its keys and values. The elements are mutable since we may change their values.



Then you bind your NSTableView to the controller.

* Select NSTableView and go to Bindings panel (&#8984;+4).
* Bind the NSTableViews content binding to the NSArrayControllers arrangedObjects binding name.

* Select the NSTableView's "content" binding in Inspector
* Set Controller Key to "arrangedObjects".
* Set Controller Key field to "arrangedObjects".

* Bind the NSTableColumn value binding to an array element selector. In this example the -description is used as the selector (all NSObjects responds to it).

* Select the NSTableColumn and select its "value" binding in Inspector.
* Set Controller Key to "arrangedObjects".
* Set Model Key Path to "description".



For illustration, you can bind the current selection to a NSTextField.

* Select the NSTextField and go to Bindings panel (&#8984;+4).
* Bind the NSTextField's value to NSArrayController's selection.description.

* Set Controller Key to "selection".
* Set Model Key Path to "description".



Lastly you set the NSTableView and NSTableColumn options for quering the controller.

* Select the NSTableColumn again, and go to the Attributes panel in the Inspector (&#8984;+1).
* Set indentifier to the selector name "description".


see also SearchingWithNSArrayController

see also ForceSortingOfAnArrayControllerHOW

see also SubclassingNSArrayController

It can also work together with an NSTableView in ways which appear magical, for example


* if a table column has its value bound to an array controller, the table view no longer needs a data source,
* If valueForKeyPath:@"arrangedObjects.name" is sent to an array controller, one (as expected) gets back an array of *name* values, but if another object replicate this behaviour, and the table column's value is instead bound to this object, it will display the entire array for each row. **(What do you mean? I don't follow.)**
----
You bind the value of an NSTableColumn to "arrangedObjects.someKey". If you try to programmatically invoke valueForKeyPath:@"arrangedObjects.someKey" on your array controller, you'll get back the array resulting from invoking valueForKey:@"someKey" on the arrangedObjects array -- this is all fine.
So one would think that NSTableColumns value can also be bound to my objects "someArray.someKey", but this will **not** work (on several levels).
----
Answer to "What do you mean? I don't follow" (as far as I understand):

Say you like to have the row's index displayed in each table row, you need to ask the array controller about it -- the single model objects cannot know about their place in the collection. You subclass NSArrayController and add a method (NSArray *)arrangedObjectsIndexes which returns a NSArray full of NSNumbers. If you now bind the NSTableColumn's "value" binding to YourArrayController's "arrangedObjectsIndexes" key, or more precisely to "arrangedObjectsIndexes.intValue", each row in your table column display the full array of numbers instead of the corresponding index.

Problem is that there are private objects communicating (like NSBinder, NSTableBinder) which seem to get the row values for the table column. The controller itself gets asked only for the "arrangedObjects" key path. You cannot override NSArrayController valueForKeyPath method and return your value for e.g. "arrangedObjects.indexInContentArray".


Can someone explain what an array controller really does? and if the table view/column is hardcoded to recognize this controller? cause it sure doesn't seem very "glue code be gone" when Apple just write a few controllers and hardwire support for these in their framework...

*But then again, if we all just write Currency Converters, then the current support is adequate!*

What would be *really* useful is a clear side-by-side comparison of how we did things pre-bindings and how we do them with bindings. For example, bindings take over the functionality of data sources. NSTableView's drag and drop methods are still declared in the NSObject (NSTableDataSource) protocol, though, so it's not clear if bindings helps at all with drag and drop; as far as I can tell, it doesn't. There's other places where bindings gives us some kind of proxy object to our data instead of the data itself, which can be really confusing if you're still trying to do some things the old way. Just when I'd figured out how to torture AppKit into doing everything I needed..

----

Question: if I have an array in the model and I want it to be displayed in an NSTableView, but items can be added to the array from other places than the GUI, is an NSArrayController the right solution? cause if I add items to the array (from another source), they will **not** show in the NSTableView, unless I issue a will/didChangeValueForKey: with my actual array being the key. I.e. if my NSTableView is set to display "arrangedObjects.name" shouldn't that then trigger a notify, when new items are added to the array?

*You can call [ myView reloadData ]; to update the display*

No, because the model which change the array do not know who is currently showing that array in the GUI -- it should happen automatically thru bindings...

----

There are a number of ways to customize new objects that get added to the array:
*Subclass NSArrayController and override **newObject** or **addObject**.
*Register as an observer of the array's key in the providing object, using KeyValueObserving. You will be informed when a new object is added to that array, which you can then modify (the new object will be passed to you.)
*In the object providing the content array, implement the KeyValueCoding array methods, one of which is **insertObject:in<Key>AtIndex:**.

*So NSArrayController will send these messages rather than simply add to the array?*

"The "newObject" trick: It apparently works, but how could anyone possibly know to do that? That method is not mentioned in any of Apple's documentation."[UPDATE: found it: http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaBindings/Concepts/CntrlContent.html]


----

The array managed by NSArrayController is bound to the "contentArray" key. To bind to an array of your choice in code rather than through the BindingsInspector do the following:
    
[arrayController bind:@"contentArray" toObject:myArrayOwner withKeyPath:@"myArray" options:/* whatever */];


This seems to work, too:
    
[arrayController setValue:myArray forKey:@"contentArray"];


And also:
    
[arrayController setContent:myArray];


----

"*** Uncaught exception: <NSInvalidArgumentException> *** -[_NSControllerObjectProxy count]: selector not recognized"

I get the above message when I use a custom wrapper around NSMutableDictionary. I'm using an NSArrayController with MyDictionary (the wrapper mentioned previously). I tried:

    
- (int)count {
    return [wrappedMutableDictionary count];
}


And it still gave the same error and crashed. Because of the "_" prefix, I'd imagine _NSControllerObjectProxy is a private class. Hence, I don't think adding a category with an empty count method will do anything. Thanks in advance for the help. -- HasanDiwan

Bindings controllers use proxies to do their stuff. You have to "unproxy" to get the original object. There is a private method for this oder another way, which I described here
http://www.cocoading.de/Common/Article.php?Area=2&Article=2
Sorry, the explanation is in german, but the code is still in Objective-C.
Amin Negm-Awad

The easiest way to unproxy soemthing is to call [something valueForKey:@"self"] instead of that longish category. And even easier, just append .self to the keypath you are observing. --DominikWagner

Proxy objects and a number of other AppKit view/controller classes (like cells or controls, and even     NSViewController) responds to     representedObject which supposedly return the "real" object behind it. Thus you can use this as a fishing hole to "dive" into until you get to the object that you want:

<pre>

id BSDiveRepresentedObject(id objectWrapper,Class soughtClass) {
	if (!objectWrapper) {
		return nil;
	} else if ([objectWrapper isKindOfClass:soughtClass]) {
		return objectWrapper;
	} else if ([objectWrapper respondsToSelector:@selector(representedObject)]) {
		id obj = [objectWrapper representedObject];
		return BSDiveRepresentedObject(obj,soughtClass);
	} else {
		return nil;
	}
}

</pre>

----

Side effect of table column <code>value</code> binding: evidently this causes each data cell to draw a background. (How exactly are value bindings and data cells related? Rhetorical question -- bindings normally creates a default cell) To fix this, just set your column's data cell to an unmodified (or modified, it doesn't matter) NSTextFieldCell. It still seems like an unnecessary step. Also, I might be wrong about the cause: my set-up is rather...unusual (I'm using an NSArrayController subclass for drag-and-drop and user defaults mutability). But *something* has to be causing this...

JediKnil

----

I experience problems with trying to implement NSUndoManager stuff with an array controller. It seems impossible instantiate an undo manager inside my NSArrayController subclass. This is following on from my investigation related to DocumentChangedStatusWithBindings.

Perhaps this means that you can only create an undo manager instance if you ask nicely. I guess this means getting one from the document.

----

I have a problem with NSArrayController : I have a NSTableView and a NSArrayController that is bound to. There are, for example, 5 values for each NSManagedObject of the NSArrayController. But what I want is to have a 6th column, calculated ,and that depends on the other Objects...

To try to be clear, each object is a financial transaction, with an amount, and I want my NSTableView to print a balance for each transaction, indicating the account balance up to this particular transaction. That is : that balance value depends on the previous transaction and the current transaction amount.

I've tried to add a instance variable in the Transaction Class :

    
+ initialize {
        ...

        NSArray *keys = [NSArray arrayWithObjects: @"amount", nil];
        [self setKeys:keys triggerChangeNotificationsForDependentKey:@"balance"];
        ...
}

- balance {
        ...
}


But from the balance method I cannot reach the previous Transaction Object. So I think what I want should be implemented in a NSArrayController subclass. 

Does anyone have an idea about it ?

----
It's slightly kludgy, but how about making your transactions into a linked list of sorts, i.e. by adding a previousTransaction connection between objects? Then you have the key path "previousTransaction.balance".

----

I want to get NSArrayController to prevent duplicates of a certain attribute. That is, instead of 

    
*Name*          *Info*
Object           InfoData
Object           InfoData
Object           InfoData
Object           InfoData


when I add entries using <code>insert:</code>, I want

    
*Name*          *Info*
Object           InfoData
Object copy      InfoData
Object copy 1    InfoData
Object copy 2    InfoData


How do I do this? I have an idea that it would involve subclassing NSArrayController and overriding <code>insert:</code>, but I'm not sure of anything beyond that. Could anyone give me specifics?

----

In your subclass do the following:

    
- (void)insert:(id)sender{
	[super insert:sender];
	id selectedObject = [self selection];
	NSString *selectedObjectName = [object valueForKey:@"info"];
	
	int count = 0;
	for(id object in [self arrangedObjects])
	{
		NSString *objectName = [object valueForKey:@"info"];
		if([objectName isEqualToString:selectedObjectName])
		{
			NSString *newName;
			if(count > 0)
				newName = [NSString stringWithFormat:@"%@ copy %d", objectName, count];
			else
				newName = [NSString stringWithFormat:@"%@ copy", objectName];
				
			[object setValue:newName forKey:@"info"];
			count++;
		}	
	}
}

You must select "Select inserted objects" in interface builder in order for this to work. I have not tested it, but it should work more less as expected.

gumoz.

----
I have a simple NSArrayController bound with a NSTableView.  I'd like to know when a cell has been edited.  I have a Model of Person and another object handling notifications of that.  I don't see how bindings help in this regard, but I don't want to know whenever my Person is edited, only the *currently selected person* (a WebView is displaying that person's url).

You can use the delegate method of your NSTableView      -tableView:shouldEditTableColumn:row:  This delegate method is called then editing begins AND when editing ends on a row.

----

I can't seem to get my NSArrayController and NSTableView to display data in my Array of objects.  What are some of the most common gotchas that new users come across when setting up these bindings?

----

When you bind the content of an NSTableView to an NSArrayController, doesn't the selection of the table automatically get bound to the selection of the array controller? 
According to the apple docs:

NSArrayController bindings
selectionIndexes

An NSIndexSet instance that specifies the indices of the selected rows in the NSArrayController.

Typically, selectionIndexes is bound automatically to the NSArrayController that the first NSTableColumn is bound to.

I have my table along with the columns bound to an array controller, but when I select any particular row [controller selection] will always return the first object in the controller.

But,
int selectedRow = [tableView selectedRow];
NSManagedObject* obj = arrayController arrangedObjects] objectAtIndex:selectedRow];

does return the proper bound object.

Any suggestions as to what is wrong?

----
Describe your bindings. What is bound to what and via what key? Be specific.

*Try using     selectedObjects.     selection is used more for one-to-one bindings.*
----
It appears that IB won't automatically bind selectionIndexes if you bind the [[NSTableView content to the NSArrayController.  However, if you just bind the column values, the automatic binding appears to work as described above.

