---
layout: page
title: StrangeDeallocIncident
---

Late note on recent page edit: *This seems largely to be a misunderstanding of how the autorelease pool works*

----

In my document-based app, my document class has a single instance variable that points
to a model object (that is itself composed of several instances). The model object is a subclass of General/NSObject;
this model object and all of its instance variables conform to General/NSCoding to archive the data.

The document class creates an instance of this model class in init, and destroys it in dealloc.
There is a setter for this variable in the document class, but I do not use it so far.
(Logging a message in the setter is never reached when opening and closing documents.)

There is a window controller that obtains a reference to the model object in its awakeFromNib, but does not retain it.

I am testing the opening and closing of documents with log statements in the init and dealloc methods of the model class.
When I open a default blank document, the alloc message is logged; when I close that window the dealloc message is
logged. In both cases the id for the model object is the same. But here is the weirdness:

When I open a document from a file on disk, I see an *additional* dealloc log message, listing a pointer to an object
that I never allocated in the init method of the model object. The usual init and dealloc log messages appear also, as
with the default blank document.

I have no idea what this other object is. It seems to come from nowhere. But I believe this is related to an additional
observation that the app leaks a dozen blocks or so when I open a document from archived data and does *not* leak
when the default blank document is opened and closed, and this is what causes me the most immediate concern.

Warning: There is an array controller (Bindings) involved, but (I think) it only copies in the objects from this model array using
its addObjects: method. Still, somehow I am inclined to blame this thing - bindings get blamed for pestilence and death, too.

I have done everything I can to search for anything that suggests a retain cycle or a circular reference, or whatever you call it,
but I come up short. I cannot find anything that should invoke this dealloc message when opening a document from a file.

Here is what the logging looks like (note the double-dealloc when opening and closing a document from disk)

2005-02-13 11:52:33.149 App[1498] alloc <General/MyModel: 0x341f10>

2005-02-13 11:52:35.070 App[1498] dealloc <General/MyModel: 0x341f10>

2005-02-13 11:52:38.688 App[1498] alloc <General/MyModel: 0x3d9fd0>

2005-02-13 11:52:39.724 App[1498] dealloc <General/MyModel: 0x3f63d0>

2005-02-13 11:52:40.737 App[1498] dealloc <General/MyModel: 0x3d9fd0>

2005-02-13 11:53:01.812 App[1498] alloc <General/MyModel: 0x3649a0>

2005-02-13 11:53:02.541 App[1498] dealloc <General/MyModel: 0x3a7240>

2005-02-13 11:53:03.640 App[1498] dealloc <General/MyModel: 0x3649a0>

2005-02-13 11:53:47.977 App[1498] alloc <General/MyModel: 0x3b55f0>

2005-02-13 11:53:49.780 App[1498] dealloc <General/MyModel: 0x3b55f0>

2005-02-13 11:54:05.149 App[1498] alloc <General/MyModel: 0x392960>

2005-02-13 11:54:05.585 App[1498] dealloc <General/MyModel: 0x3ba2e0>

2005-02-13 11:54:06.674 App[1498] dealloc <General/MyModel: 0x392960>

----

I should preface this by saying I don't really know what might be going on.  Here's something to look at though:  when decoding archives, it's possible for substitutions to occur after decoding the object graph.  This could explain an extra object created and destroyed (maybe an instance of the General/NSNotifying subclass is substituted or something).  As for the missing alloc, well, maybe the alloc method is not necessarily called when you decode an object from a nib.  It's possible that it's done more directly.

*Possibility:     alloc just calls     allocWithZone: with the default zone. Maybe     allocWithZone: is being called instead [edit: directly, not instead, probably from unarchiveObjectWithData:, now that you've posted your code] here. Also, is there any real problem with this, or are you just curious? --General/JediKnil*

----

Here are the relevant methods from the document, model, and window controller classes
(This is largely boilerplate code - so far, the app just undoably adds and deletes records from its table view,
loads and saves documents, or imports and exports data from and to text files. All of it is taken from published sample code.)
I have changed the identifiers below to preserve generality, and might have integrated it all in some idiosyncratic way:

**Document Class**

    
- ( id ) init {
    if ( self = [ super init ] )  {
        myModel = [ [ General/MyModel alloc ] initWithDocument: self ];
    }
    return self;
}

- ( void ) dealloc {
     [ myModel release ];
     [ super dealloc ];
}

- ( General/NSData * ) dataRepresentationOfType: ( General/NSString * ) type  {
        General/MyWindowController *winCtl = [ [ self windowControllers ] objectAtIndex: 0 ];
        General/NSRect frameRect = [ [ winCtl window ] frame ];
        float toolbarHeight = [ winCtl toolbarHeightForWindow: [ winCtl window ] ];     // Apple documentation on toolbars presents this
        General/NSRect saveRect = General/NSMakeRect( frameRect.origin.x, frameRect.origin.y + toolbarHeight,
            frameRect.size.width, frameRect.size.height - toolbarHeight );

       [ myModel setFrame: saveRect ];
     
        // Get the current data from the window controller before archiving
       [ myModel setInstanceArray: [ winCtl instanceArray ] ];  // Yes, the arrays use the same identifier in model and window controller
	// However, setInstanceArray in the model class uses the copy method, so we should point to different objects in each class
	if ( [ type isEqualToString: @"My Kind Of Document" ]  {
              return [ General/NSArchiver archivedDataWithRootObject: [ self myModel ] ];
	}
	else  {
              return nil;
	}
}

- ( BOOL ) loadDataRepresentation: ( General/NSData * ) data ofType: ( General/NSString * ) type 
	General/MyModel *model = nil;
	if ( [ type isEqualToString: @"My Kind Of Document" ] )  {
		model = [ General/NSUnarchiver unarchiveObjectWithData: data ];    // I assume this instance will be autoreleased
		if ( model != nil )  {
			[ myModel setFirstLabel: [ model firstLabel ] ];
			[ myModel setSecondLabel: [ model secondLabel ] ];
			[ myModel setThirdLabel: [ model thirdLabel ] ];
			[ myModel setInstanceArray: [ model instanceArray ] ];
			[ myModel setFrame: [ model frame ] ];
			return YES;
		}
		else
			return NO;        // The General/NSUnarchiver failed
	}
	else
		return NO;       // The document was of the wrong type
}


Here are the relevant methods for the General/MyModel class referred to in the above code (again, nothing fancy);
the style I use for my setter accessors is to     release the current instance
in first line and     retain the argument passed in in the second.

**Model Class**

Note especially that the model class does not instantiate a mutable array, but a non-mutable copy, and only for archiving.
The user is never directly modifying the model class. The array is copied in or out when archiving and unarchiving.

    
- ( id ) init   {
	return [ self initWithDocument: nil ];
}

- ( id ) initWithDocument: ( General/DDVDocument * ) myDocument    {        // Designated initializer; maintains a reference to the document
	if ( self = [ super init ]    {
		document = myDocument;     // This will be worthless if the document somehow closes before the model is released
		instanceArray = [ [ General/NSMutableArray alloc ] init ];
                // These are default labels for the table view header cells that the user can modify in the interface, specific to each document
		firstLabel = [ [ General/NSString alloc ] initWithString: @"First Column" ];
		secondLabel = [ [ General/NSString alloc ] initWithString: @"Second Column" ];
		thirdLabel = [ [ General/NSString alloc ] initWithString: @"Third Column" ];     // No 'fifth columnists' allowed  ;-)
	}
	return self;
}

- ( void ) dealloc    {
	[ instanceArray release ];
	[ firstLabel release ];
	[ secondLabel release ];
	[ thirdLabel release ];
	[ super dealloc ];
}

- ( void ) encodeWithCoder: ( General/NSCoder * ) encoder {
	[ encoder encodeRect: frame ];
	[ encoder encodeObject: instanceArray ];
	[ encoder encodeObject: firstLabel ];
	[ encoder encodeObject: secondLabel ];
	[ encoder encodeObject: thirdLabel ];
}

- ( id ) initWithCoder: ( General/NSCoder * ) decoder {
	if ( self = [ super init ] )   {
		[ self setFrame: [ decoder decodeRect ] ];
		[ self setInstanceArray: [ decoder decodeObject ] ];
		[ self setFirstLabel: [ decoder decodeObject ] ];
		[ self setSecondLabel: [ decoder decodeObject ] ];
		[ self setThirdLabel: [ decoder decodeObject ] ];
	}
	return self;
}


**Window Controller Class**

(By the way, if we can fix this, I believe it provides a nice template for a simple table app; all you need do is
implement some indexed accessors for the instance array that belongs to the window controller, where in
you forward invocations for the use of the undo manager and implement your Key Value Observing for appropriate key paths):

    
- ( id ) init    {
	if ( self = [ super initWithWindowNibName: @"General/MyDocument" ] )
	{
		instanceArray = [ [ General/NSMutableArray alloc ] init ];
		[ self setShouldCloseDocument: YES ];   // close entire document if main window closes
	}
	return self;
}

- ( void ) dealloc  {
	[ [ General/NSNotificationCenter defaultCenter ] removeObserver: self ];
	[ searchField setDelegate: nil ];
	[ self setInstanceArray: nil ];    // This accessor has to go through the additional step of canceling observers for table row objects
	[ super dealloc ];
}

- ( void ) awakeFromNib    {
	General/MyModel *model = [ [ self document ] myModel ];
	
	if ( [ model hasFrame ] )     {
		[ self setShouldCascadeWindows: NO ];      // This will allow restoring the frame of the document window, loaded from archive
		[ [ self window ] setFrame: [ model frame ] display: YES ];
	}
	
	[ self setupToolbar ];
        // The outlet arrayController points to an instance of General/NSArrayController in the nib file
        // This app uses manual bindings to bind the array controller to this instance array in the window controller
	[ arrayController bind: @"contentArray" toObject: self withKeyPath: @"instanceArray" options: nil ];
        // I am assuming that the binding takes care of keeping the instanceArray consistent with the arrayController content
	
	// Bypass undo registration when loading archived data from a file
	[ [ [ self document ] undoManager ] disableUndoRegistration ];
	[ arrayController addObjects: [ model instanceArray ] ];
	[ [ [ self document ] undoManager ] enableUndoRegistration ];
	
	//The table view columns are bound to the array controller keys in IB
        [ tableView deselectAll: self ];
	[ tableView reloadData ];
}

// The manual binding and unbinding of the array controller
// are crucial to avoid a retain cycle associated with the binding when done through File's Owner in the nib
- ( void ) windowWillClose: ( General/NSNotification * ) note  {
	[ arrayController unbind: @"contentArray" ];
	[ arrayController setContent: nil ];
}

- ( void ) setInstanceArray: ( General/NSMutableArray * ) array {
	General/NSEnumerator *e;
	Item *item;
	
	if ( array == instanceArray )
		return;
	
	e = [ instanceArray objectEnumerator ];
	while ( item = [ e nextObject ] )
		[ self stopObservingItem: item ];      // Observation of the relevant keys in the Item class that the user can value-modify
	
	[ instanceArray release ];
	instanceArray = [ array retain ];
	e = [ instanceArray objectEnumerator ];
	while ( item = [ e nextObject ] )
		[ self startObservingItem: item ];      // Observation of the relevant keys in the Item class that the user can value-modify
}


----

myModel is an instance of the General/MyModel class, and is alloc'd in the init method of the document class. The initializer of the General/MyModel class
is presented in the description above.

So the dealloc that I am seeing prior to the "real" one is courtesy of the autorelease pool.
I notice on the "anomalous" one that at the time the dealloc happens, none of the instance variables point to anything. Makes sense now.
Well - at least I feel confident that this business has nothing to do with my memory leaks.

I personally don't have the kind of luck that allows my objects to survive a double-dealloc without causing a crash!

----

I'd recommend that you override -dealloc in your General/MyModel class if you haven't already, and put a breakpoint on it. That way you can see exactly where it's being called and what its contents are at the time.

----

Having that breakpoint in dealloc in the model class was how I first "discovered" that it was being called twice.
I had not thought about logging from dealloc since I did my first experiments with autoreleased objects as a beginner.
I am convinced that the first dealloc comes when the temporary object is taken out by the autorelease pool.
At that point the document ivar points to nothing, but the rest of the model ivars contain the stuff from the General/NSUnarchiver.
This is as it should be; my mistake. The second dealloc shows the document ivar points to the current document.

Here's the loadDataRepresentation: ofType: method from the document class again:

    
- ( BOOL ) loadDataRepresentation: ( General/NSData * ) data ofType: ( General/NSString * ) type   {
	General/MyModel *model = nil;
	if ( [ type isEqualToString: @"My Kind of Document" ] )     {
		model = [ General/NSUnarchiver unarchiveObjectWithData: data ];
		if ( model != nil )     {
			[ myModel setFirstLabel: [ model firstLabel ] ];
			[ myModel setSecondLabel: [ model secondLabel ] ];
			[ myModel setThirdLabel: [ model thirdLabel ] ];
			[ myModel setInstanceArray: [ model instanceArray ] ];
			[ myModel setFrame: [ model frame ] ];
			return YES;
		}
		else
			return NO;
	}
	else
		return NO;
}


----

In general, my setter accessors follow this pattern

    
- ( void ) setInstanceArray: ( General/NSArray * ) array
{
	[ instanceArray autorelease ];
	instanceArray = [ array copy ];       // Immutable object - the model is never supposed to change the contents of this array
}

