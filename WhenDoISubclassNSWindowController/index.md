---
layout: page
title: WhenDoISubclassNSWindowController
---

In the app I am writing (a document based application) I was originally going to write my application using a subclass of General/NSWindowController to control on-screen elements and their connections to the data in the application.  Before a Save was executed by the user I was going to synchronize my data objects from the General/NSWindowController to similar objects in the document itself, then encode the document and save it.  The reverse would be true on an Open operation.

After much discussion, I was convinced by various programmers on more than one forum to scrap the seperate General/NSWindowController and simply use the document itself to control the window--that way I would only need one set of data at any one time and would not have to synchronize the controller's (and thus the application's) data to the document's data objects before encoding (and the reverse).

What I'm finding, now, though, is that since my application is filling out and I have quite a number of on-screen elements--including many General/IBActions from buttons, that my document is acting like more of a controller and less of a document.  I am also finding that I am writing many data-handling methods that simply just don't seem to belong in a document.

Of course, this all works in the current incarnation of my application, but it seems kludgy.  I am starting to think that my original concept may have seperated the control in a better manner.

I am wondering what all of your thoughts are on this matter.  How large does YOUR application have to be before you decide to subclass General/NSWindowController?  What other factors come into play before YOU subclass General/NSWindowController?

As well, when you do use a subclass of General/NSWindowController, how do you go about synchronizing your data to the document before saving/loading the data using dataRepresentationOfType and loadDataRepresentation:ofType: in a document based app?  

I suppose one process would be to give the General/NSWindowController object the ability to encode the data and simply have the document request the coded General/NSData object for Saving (and the reverse for Opening) via an accessor method when required.  Or the General/NSWindowController could have a "synchronize" method that copies the data to the document via its accessors.  

What do YOU do?

Thanks everyone.  I am seriously considering re-organizing my application, but it will take a LOT of work at this point (already) and I don't want to proceed unless I see overwhelming support for the sublassed General/NSWindowController model.

I look very forward to any and all discussion on this matter.

General/KentSignorini

----

I am not sure about General/NSWindowController IMHO it's main purpose is to control the **Window**, not the views it contains. I usually use a plain General/NSObject as the controller for the UI elements in the window. I don't know if this is a good thing, and if your General/NSDocument class is getting to large you might have to do somehting anyway. Most of the times when I subclass one of the Appkit classes to extend its functionality i find that I really did not need to.

General/HaRald

----

General/HaRald: OK, so assume that you use a subclass of General/NSObject to act as a controller, where do you keep your data--in the subclassed General/NSObject that you created?  In the document itself?  How do you synchronize before/after a Save/Open?

General/KentSignorini

----

The data is an object on its own (the Model in General/ModelViewController), the General/NSDocument object always keeps a reference to this object and this is they way most of my supporting classes get access to the data <code>General/[[[NSDocumentController sharedDocumentController] currentDocument]</code> gets you to your document object. Another way could be to instatiate the controllers in the NIB file and create a connection between the Owner of the document window (your subclass of General/NSDocument) and the controller instance.  I really don't know if this is a good thing, but it seems that when there are a lot of user interface elements in one view the controller classes just tend to accumulate a lot of clutter. I was trying to use small control objects for the views in the document window and not clump everything into one.

General/HaRald

----

OK, but I still don't know how you get your document to save the data.  Is the data object instantiated by the document and you just encode with the document being the root object?

I mean, somewhere in your code you create an instance of your data object (model) and instances of the controller(s) object(s) that you're using, right?  So where do you create the instancw of a controller object if it's not being done automatically (as it would be if your controllers were subclasses of General/NSWindowController instead of just General/NSObject)?  And as I asked, above, where do you create the data object, as well?

I'm just trying to get my head around how other people do this.

Does anyone else have examples of what they do, too?  The more the better...

Thanks,

General/KentSignorini

----

First of all, General/NSDocument is not a model class. Technically, it is called a model-controller. A model-controller's main goal is to manage one or more model objects - keeping them up to date with the view, etc. A view-controller's primary goal (such as an General/NSWindowController subclass) is just the same except it manages view objects. For more information on this, see the docs:

http://developer.apple.com/documentation/Cocoa/Conceptual/General/AppArchitecture/Concepts/MVC.html

Now, how you split this up really determines on the size of your program. If the document is extremely simple, you can just put everything in the document class. However, I find it best, even with simple applications, to have an General/NSWindowController manage the interface. If things get any more complicated, you can split the window controller into smaller view controllers. For example, if you have an General/NSTableView that is doing a lot, you can create a General/TableViewController class that manages the table, keeping it up to date. Even if the interface is split up into several tiny objects, I still prefer to cannel all communications through the window controller - that way the model-controllers don't need to know everything about the tiny view controllers. You can do the same thing with the document, you may wish to have several model-controllers if it is too complicated.

With this in mind, you would want to instantiate all view-controllers using Interface Builder so they can communicate freely with the interface. I try to keep the model-controllers out of the Nib file, but a document is an exception in this case.

A problem arises - how do you keep the model in sync with the view? There are several ways to do this and it really depends upon the program you are creating.

The simplest way is for the document to not even have model instance variables. Instead, it creates all the model objects inside the "dataRepresentationOfType:" method by retrieving the data from the view. For example, if you were creating a text editing application, you could just grab the RTF data from the General/NSTextView - simple. In more complicated applications this approach will not work.

Another method (which works well for me) is to have the model be updated whenever the user submits data, and to have the view be updated whenever the model changes - this ends up being a chain from the view (user input), to the model, then back to the view. Let's take a look at a detailed example. Say there is a table view of names and a text field for the user to add a name. For simplicity, I will say there's only a window controller and a document. If there are tinier controllers then this chain would simply be longer. Here's the chain of commands:

* The user types in a name then clicks the "add" button. This calls the window controller's "add:" Interface Builder action.
* The window controller retrieves the name from the text field and calls the "addName:name" method in the document.
* The document updates its General/NSMutableArray by calling "addObject:name"
* The document then calls "updateNameTable" inside the window controller because the model changed.
* The window controller calls "reloadData:self" in its name table.
* The window controller's table delegate methods retrieve the array from the document and pass it along to the table view so it knows how to update the view.


In this example the model data is always up to date so saving the data would be as simple as returning the archived array. Upon opening a document, you just unarchive the array and call "updateNameTable." One important thing to note is that upon saving the document, you should only save the model objects inside the document. Don't archive the entire document object and return it.

Sometimes it is not desirable for the model-controller to know about the views. I find it works very well if the model-controller sends out an General/NSNotification whenever it updates a model object. The appropriate view controller then listens for the notification and updates its view accordingly. This allows more flexibility and reusability in your code.

(Sorry for this very long ramble, let me know if something doesn't make sense. I'd be more than happy to give you an even longer explanation.) : )

-- General/RyanBates

---- 

Actually, this is very helpful.

But could you explain the last paragraph regarding sending a notification, etc.?

Thanks again,

General/KentSignorini

----

Okay, I feel like writing a lot tonight, so you asked for it! ; )

In Object Oriented Programming, it is often best to make objects as encapsulated as possible (the less they know about other objects the better). The two extremes: Model and View classes should be the most encapsulated. Encapsulated classes usually lead to a better OOP design.

Controller objects, cannot be nearly as encapsulated as the Model and View objects because they contain more application-specific code that interacts closely with other View, Model and Controller objects. In fact, it's not as important to encapsulate the controller objects either.

Notifications is one way to improve the encapsulation of objects. A notification's primary use is to tell other objects that something occurred so these objects can react to the change. One thing to note, the object sending the notification should not rely on another object to listen and respond to the notification. This is better suited for a direct message.

It may help to think of notifications as a radio broadcast an object sends out. Any other objects can tune in and listen to what that object is doing. The broadcaster doesn't know how many objects are listening - maybe no objects are listening - it just sends the notification.

This works perfectly for model controllers because they can broadcast something like: "I added a name to the names array!" The view controllers can listen in to the broadcast and update the view accordingly. I find notifications to be more helpful if you have a lot of tiny controllers. For example, a General/NameTableController might listen to notifications from a General/NameArrayController so it can update itself whenever the names array changes.

You can really go overboard with notifications; and, honestly, they are only worth using in certain cases. Messaging directly to the object is usually sufficient enough, but you may consider trying out this idea with notifications to see if you like it. One down side is it requires quite a bit of code to observe and send notifications. It might be a little slower as well. Here's an example of observing and sending a notification:

    
// Inside a General/NameTableController class
// let's observe the notification "General/NameArrayDidChange" from the nameArrayController object
// It will call the "updateTable" method in this object whenever the notification is sent out
// NOTE: we need a corrosponding remove observer message before this object is deallocated.
General/[[NSNotificationCenter defaultCenter] addObserver:self
		selector:@selector(updateTable)
		name:@"General/NameArrayDidChange" object:nameArrayController];

// After we changed the name array in the General/NameArrayController, we can send out the notification:
// This will end up calling the "updateTable" method in the General/NameTableController class
// but this class knows nothing about that class
General/[[NSNotificationCenter defaultCenter]
		postNotificationName:@"General/NameArrayDidChange" object:self];


The "defaultCenter" handles sending the correct notifications to the observers. When observing a notification, if you specify "name:nil" then it will respond to all notifications from that object no matter what the name is. In the same manner, if you set "object:nil" it will respond to all notifications with the name no matter what object it came from. Kind of neat. To learn more about notifications, check out the documention:

http://developer.apple.com/documentation/Cocoa/Conceptual/Notifications/index.html

(this might fit better in a different page - anyone, feel free to move it if you want, but please provide a link to it here).

-- General/RyanBates

----

Ryan's contributions are excellent and I can hardly contribute any more, but I have a need to try.

Regarding the controller layer in MVC, I would go so far as to assert that, ideally, the Controller class(es) has (have) all the app-specific code and nothing except app-specific code. In a way, the controller layer is, conceptually, a single behaviour-based object � but that's unmanageable in all but the smallest applications. So you divide it up, partitioning chunks of related behaviour based on the other objects/data being controlled: model objects, U.I. objects, preferences, inter-application communication, what-have-you. Usually the document specializes in managing the model � the model and the document's data being one and the same. But because you need some kind of unified object graph, usually the document is the base of the large branch of objects (model, U.I., the gunk that links them) which manage a single document � document objects are in turn managed by the document controller, which in turn is managed by the application object, etc.

The document, then, has two main characteristics: a bunch of methods to control the model as a whole (file stuff, mostly), and a bunch of methods to co-ordinate the other controllers (like window controllers, data sources, and various delegate objects ��frequently also found in the document nib), which do everything else. The document also needs references to those additional controllers, in addition to at least one reference to the model. It has to do a lot of setup in the beginning, linking various other model controllers to their special parts of the model, and other view controllers to whatever model controllers they have to talk to. Liberal use of notifications can reduce the number of branches in your object graph, and avoid messy tangles.

I think a good goal to keep in mind, while developing, is how to avoid the situation where too many controllers are always having to coordinate with other controllers. Try to find natural divisions in the areas of your application and emphasize them, and avoid designing classes that cross those lines. This can be difficult in some cases, such as when you need to have controllers that don't inherit from General/NSResponder (i.e.: not window controllers) responding to menu items. You either need a lot of wrapper methods in your window controllers and document, or have to use something advanced like invocations. But if you can assign different model controllers to various parts of the model, pair them up with related view controllers, and link them up either in the nib or when the document is opened, you can usually keep things fairly clean. � General/BrentGulanowski

----

When you say, link them up "...when the document is opened..." are you referring to creating references in the General/NSWindowController that point to data objects in the document and/or setting the "content" of General/NSArrayControllers, etc. also to data objects in the document itself?  To me, this seems the natural approach and it removes the necessity to coordinate/synchronize data between controllers.

In effect, each controller is manipulating data objects that reside inside their document so that when the document methods for encoding/decoding/saving/opening run, they can work with data that is already a part of the document they belong to.  This is what I've come up with after reading many posts on this subject.  

Am I far off?  Is this what you are referring to?

Thanks again,

General/KentSignorini
 
----

-- I myself am completely ignorant of the Controller architecture still -- just haven't found time to learn it yet. I guess I'm going to have to since that's the way things are (quickly!) moving. But basically I think you understood me. You have to make or wake up controllers, and you have to either a) call -setIvar: in the controllers, or they will call -ivar in the document � one way or another the view and the model have to connect. All the work is finding the most sensible way to connect them, based on the kind of model you're working with. It's like there's all this little wires, one from every user interface control, creating communication channels through the controller layer into the model's interface. I guess the new Controller architecture helps to standardize the layout of all of that wiring. �Brent

----

Rather than use notifications to let the views or view-controllers know when a model object has changed, you can use General/KeyValueObserving (which is how General/CocoaBindings does it).

----

Well, as far as General/CocoaBindings and using the new General/NSArrayController class, etc., I believe I have a good handle on how they work.  In fact, my current program attaches an General/NSArrayController I built in the NIB file to an array in the document and everything works just fine.

But, back to my original problem, I want to split my app between a document (holding the data and encoding/decoding/saving/opening methods) and an General/NSWindowController (or more than one) to handle all the view-controller type stuff.  

So, that said, can my General/NSWindowControllers contain pointers to the document data in such a way?:

    
General/MyDocumentClassName *theDoc = [self document];
General/NSString *someStringDataVariable = [theDoc someStringDataVariablesAccessorMethod-
General/ThatReturnsAnNSStringPointerToTheACTUALDataInTheDocument];
someStringDataVariable = @"This is setting the value of the ACTUAL variable in the document!";



And can my document point to its General/NSWindowControllers' data in such a way (probably would never have to do this though, as the General/NSWindowController and the other General/NSArrayControllers, etc. do all the handling of moving data between on-screen elements and the data variables in the document, anyway)?:

    
int i = 0; // 0 is the first General/NSWindowController, and 1 would be the second, etc.
General/NSWindowController *theFirstWindowController = General/self windowControllers] objectAtIndex:i];
[[NSString *someStringDataVariable = [theFirstWindowController someStringDataVariableAccessor-
General/MethodSimilarToTheOneAbove];
someStringDataVariable = @"This also sets the value of the ACTUAL variable, but this time it's"
"in the window controller's data";


Does this look about right?  Especially the first example (the General/NSWindowController accessing variables in its associated document)?


Thanks again, everyone!

General/KentSignorini

----

If you are trying to set the actual data of the document, no, this will not work. I suggest brushing up on the concept of pointers/references. Since this page is off topic anyway, I may as well include a brief explenation here. :)

A pointer does not actually contain the data, it simply points to the data. Let's take a simple example:

    
General/NSString *firstString;
General/NSString *secondString;

firstString = @"Hello from firstString!"; // 1
secondString = firstString; // 2
secondString = @"Hello from secondString!"; // 3


Now for a pop quiz, what will be the value of firstString? It will be "Hello from firstString!" because you never alter the data the first string points to. At comment 2, you are telling the second string to point to the same data as the first string. However, at comment 3 you are telling the second string to point to another string - not changing the value of the first string.

If we look at your question, you can see you are **not** setting the document's data, simply setting which string "someStringDataVariable" is pointing to. In order to set the documents data, you need to create accessor methods. For example, you can add this method to your document class so other objects can change the data.

    
- (void)setSomeString:(General/NSString *)newString
{
	[someString autorelease]
	someString = [newString copy];
}


-- General/RyanBates

----

I think your example is what I am trying to accomplish.  I guess my intent is to create a new pointer in the General/NSWindowController that points to the same location in memory as the data in the document so that I can use the new pointer in the General/NSWindowController the way I would use the original in the document itself.

OK, I'm thinking out loud here, so bear with me...

Say, in the document itself, there are two variables declared as so: 

    
// allocate an General/NSString object  and assign it a pointer called "aString"
General/NSString *aString = @"a string of text";
// create a new pointer called "anotherStringPointer"
// and assign it the same address as that of "aString" 
General/NSString *anotherStringPointer = aString;


What I would expect is that both aString and anotherStringPointer will point to the same memory location holding the string "a string of text".  Because using the @"" notation is being used as the object alloc and initializer methods for the General/NSString object.  This is like your example, right?

So just for fun, let's forget General/NSStrings for a second and pretend these are General/NSMutableArrays defined as so:

    
General/NSMutableArray *anArray = General/[[NSMutableArray alloc] init];
General/NSMutableArray *anotherArrayPointer = anArray;
General/NSNumber *theNumber = General/[[NSNumber alloc] initWithInt:5];

[anotherArrayPointer insertObjectt:theNumber atIndex:0];


Now, won't [anotherArrayPointer objectAtIndex:0] and [anArray objectAtIndex:0] both return the General/NSNumber object with the value 5?  Wouldn't they be pointing to the exact same General/NSNumber object?

So now let's assume that we want a pointer in the General/NSWindowController to point to a data object (an General/NSNumber this time) in the document.  Here is the code in the document:

    
General/NSNumber *theNumber = General/[[NSNumber alloc] initWithInt:5];

- (General/NSNumber)returnTheNumber
{
    return theNumber;  // Doesn't this actually return the address to the data object
// called "theNumber" stored in the pointer variable theNumber?
}



And then in the General/NSWindowController:

    
General/MyDocumentClassName *theDoc = [self document];
General/NSString *aPointerToTheNumber = [theDoc returnTheNumber];
General/NSLog(@"The number is: %i", [aPointerToTheNumber intValue]);


Won't this output the number 5 to the log?

I think I've got my pointer logic correct if I'm understand Kernighan and Ritchie 2nd Ed. pg. 95 correctly:

    
int x = 1; // paraphrased from pg. 94
int *ip; // from pg. 94
ip = &x; // from pg. 94
int *iq; // infered in the text below

iq = ip; // from pg. 95


"Finally, since pointers are variables, they can be used without dereferencing.  For example, if iq is another pointer to int, iq = ip copies the contents of ip into iq, thus making iq point to whatever ip pointed to."

Following this logic, is there anything that won't work in what I've done (General/NSWindowController example), above?

Thanks for hanging in there with me on this.

General/KentSignorini

----

Looks like you understand pointers and you have the right idea with General/NSMutableArray and General/NSNumber; however, General/NSString is still incorrect. First of all, General/NSString is an immutable object, meaning you cannot change the value of it. If you want to change the value of a string object, you will need to use General/NSMutableString.

So, you are actually creating a pointer to a **new** General/NSString object when calling     @"a string of text" - it is not used as an accessor method for altering the value of an already established string.

I'll compare the General/NSString with an General/NSNumber so you can see what it's doing:

    
General/NSString *firstString = @"Hello!";
General/NSString *secondString = firstString;
secondString = @"Hello again!";

// Will return "Hello!"
General/NSLog(@"%@", firstString");

General/NSNumber *firstNumber = General/[NSNumber numberWithInt:5];
General/NSNumber *secondNumber = firstNumber;
secondNumber = General/[NSNumber numberWithInt:7];

// Will return "5"
General/NSLog(@"%@", firstNumber);


-- General/RyanBates

----

OK, well first of all, right-o about the General/NSStrings and not using General/NSMutableString.  I don't know what I was thinking.  Also, I messed up (above) and one of my examples above should have read:

    
General/MyDocumentClassName *theDoc = [self document];
**General/NSNumber** *aPointerToTheNumber = [theDoc returnTheNumber];
General/NSLog(@"The number is: %i", [aPointerToTheNumber intValue]);


instead of saying "General/NSString" in the second line.

Now, your General/NSNumber example would end up with firstNumber equalling 5 and secondNumber equalling 7 because your third line creates a new General/NSNumber object and re-assigns the secondNumber pointer to the new object, right?

But, if you left out your line 3 so that your code simply said the following:

    
General/NSNumber *firstNumber = General/[NSNumber numberWithInt:5];
General/NSNumber *secondNumber = firstNumber;

// Will return "5"
General/NSLog(@"%@", firstNumber);


But then added:

    
General/NSLog(@"%@", secondNumber);


Should this not ALSO output the number "5"!?


So, if I use the below example to access General/NSMutableArrays in my document from the General/NSWindowController, this should work--provided I use the accessor methods for General/NSMutableArray to set/return values, correct?

    
General/MyDocumentClassName *theDoc = [self document];
// my document will have a method to return the pointer to the given array
General/NSMutableArray *aPointerToTheArray = [theDoc returnTheArray];
General/NSNumber *aTestNumber = General/[[NSNumber alloc] initWithInt:5];
[aPointerToTheArray insertObject:aTestNumber atIndex:0];
General/NSLog(@"The number I just stored in the document's array by pointing to it from"
"inside the General/NSWindowController is: %@", [aPointerToTheArray objectAtIndex:0]);



Thanks again (General/RyanBates, especially),

General/KentSignorini

----

Now, back to my original, original line of questioning:

In YOUR (the plural you) document based apps with seperate General/NSDocument s and seperate General/NSWindowController s, where do YOU store your data and how do you synchronize it all before/after saving/loading, etc.?

Anyone else have other procedures that they use?

General/KentSignorini

----
General/KeyValueObserving and General/CocoaBindings were really made for this. They'll even take care of Undo support for you with a little work. Contact me (see my user page) if you need any help. --General/BenStiglitz
