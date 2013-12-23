---
layout: page
title: ArraysBlankInCodingCommands
---



Program is based on a series of NSMutableArray (P0-P11 on the code below). They're global, and given a retain message upon initialization. If I print them out via <array> description] UTF8String] in either the initWithCoder, encodeWithCoder, or the methods for saving and loaded data representations, they always come out blank. Arrays only get a release message in my dealloc statement. Saved document loads without crashing but always comes up blank since the arrays are encoded empty. I've put print statements in other methods I've made in the code, and the arrays print out with their proper values. It seems only the methods responsible for saving and opening see them as blank.

I have a bunch of other methods that deal with my program's data manipulation, and that's where the arrays get their data. I know that the arrays are going into these saving methods full because I have a switch on my GUI that prints them out, and they always come out full. It's just in the archiving/unarchiving methods they appear empty.

- Kenneth Hoffmann

----

The document controller has a pointer to your window, potential window controllers do to. It is this pointer you would need to update, if you wanted to simply "replace" your document object.

----

This is my data method (the program is using the [[NSDocument architecture). 'model' is a instance of a class called MatrixData which I've implemented. It holds 13 NSMutableArray instances used by the program. This method comes from the MyDocument that MatrixData stores arrays for.

    
- (NSData *)dataRepresentationOfType:(NSString *)aType
{
	NSMutableArray * test = [[NSMutableArray alloc] init];
	[test addObject:@"AH"];
	printf("Setting:\n");

	/*
		'model' is a instance of a class called MatrixData.
		This should set a MutableArray in the MatrixData class to test. 
		I have a print statement in the model class to see if this gets
		executed - and it doesn't. It's like this line is getting skipped.
	*/

	[model setRow:test rowNum:0];

	//This prints out null. It should return the test I just made.

        printf("P0 in data. %s\n", [model returnRow:0] description] UTF8String]);

	if([aType isEqualToString:@"Matriosity Matrix"]) {
		printf("Coding\n"); //This line executes
		return [[[NSKeyedArchiver archivedDataWithRootObject:[self myData]];
		/*
		myData returns 'model'. I've put a print statement
		in encodeWithCoder, and it's not getting executed. Apparently nothing is saved.
		Save and Open don't crash, but Open just gives me a new blank document.
		*/
	}
	printf("Returning nil.\n");
	return nil;
}



----
You mention within the comments of your source that the following,     [model setRow:test rowNum:0]; looks like it is being skipped. This is your first clue that something is already wrong, and it most likely does not have much to do with the encoding (yet). I would venture a guess that     model is nil at the time of this call, or     - setRow:rowNum: does not exist as a method.

----

It's a lot like the method sees everything as null. 'model' is a global variable, and unless the method somehow is getting called before the save command, there's no way it could be nil. Any value I try to print out in loadDataRepresentation or the above method comes out as nil or as blank, even though they're all global, declared in the .h file and instantiated in a method that gets called before the save command.

I declared a new      MyDocument  called temp and unarchived into that instead. It didn't make a visible difference in execution, however.

- Kenneth Hoffmann
----
And what did you do *after* un-archiving into the temporary?  Just post the code for     loadDataRepresentation:ofType:.

----

Here's loadData. I've changed this a bit. setData sets the MatrixData object for the document (the thing that was saved before). displayMatrix refreshes the window. Unfortunately, if I print out an array from the MatrixData object after loading using printf, it comes out not as null, but as blank.
    
- (BOOL)loadDataRepresentation:(NSData *)matr ofType:(NSString *)aType
{
	[self setData:[NSKeyedUnarchiver unarchiveObjectWithData:matr]];
        [self displayMatrix];
	return YES;
}


----
*> 'model' is a global variable*

Hmm, I smell trouble. Is this a document based application?

*> even though they're all global, declared in the .h file and instantiated in a method that gets called before the save command*

How are these declared in the .h file? Are the globals prototyped as *extern* in the .h file and then defined/initialized in the .m? Or by saying that they are global, do you mean that these are class instance variables?

----
It is a document-based application.  'model' is declared in the MyDocument class .h file,      MatrixData * model; with no extern tag, and initialized and instantiated in the .m  file, in a method called generateMatrix which gets called when the user asks the program to do something (it's a simple program). At first, I was doing the initializing and instantiating in MyDocument's init method, but I read another topic here about saving where the open command was calling the class' init and wiping the loaded variables clean, so I moved it.
----
I think there is a few problems with your design. For example *generateMatrix [...] gets called when the user asks the program to do something*, how can you be sure that this is *before*     dataRepresentationOfType:?

Do all documents share the same model? In that case, a proper place to put it is probably in the MainMenu.nib.

*Or in a NSDocumentController subclass*
----
I agree, I think some design review is in order to hash this out properly.

> *It is a document-based application. 'model' is declared in the MyDocument class .h file with no extern tag.*

Technically, that's not safe, as every file to include/import "MyDocument.h" will get a fresh (read uninitialized) MatrixData pointer named *model*. The compiler/linker may complain, but that would be a fairly project specific assumption.

If you want to keep it in the MyDocument files; I would move it out of the .h file or tag it with extern in the .h file and add it to the .m like so.

*MyDocument.h*

    extern MatrixData * model;

*MyDocument.m*

    MatrixData * model = nil;

----
MyDocument is the main class. It's the one doing the saving. However, when I try to print the data there, it comes out blank. It also comes out blank in MatrixData's encodeWithCoder method, which is doing the archiving.

-KH
----
It seems that in many cases you are accessing the P0-n arrays in MyDocument and not in MatrixData, but it is the MatrixData object which you encode and later decode.

----
I would strongly suggest that rather than spending your time trying to find the problem, you simply start from scratch with a "cleaner" design. I.e. where there is only the model object and not half the code in MyDocument and the other half in MatrixData.

I can ensure you that the encode/decode methods do work.

Let me also suggest that you use C-arrays for the matrix, e.g.:
    
#define NUMBER_OF_ROWS 10

@interface MatrixData
{
   NSMutableArray* rows[NUMBER_OF_ROWS];
   ...
}
...
@end


In code you can then simply do:
    
for(size_t i = 0; i != sizeofA(rows); ++i)  // sizeofA is from /usr/include/c.h
   rows[i] = [[NSMutableArray alloc] init];

Which, judging from your code, will remove **a lot** of lines.

I also noticed that your code passes NSMutableArray around and retains it when it needs to keep it. This is not very safe, as the caller may later change the array (which I think is actually the case in your code).

----
I created a new project and rebuilt a lot of it. This got rid of a lot of weird errors, but not the save error. I cleaned it up so that MatrixData is the model, MyDocument the controller, etc just have methods for calculation that MyDocument actually prints the screen. Now, saving works just by having MyDocument archive the MatrixData object with a NSKeyedArchiver. Unfortunately, the data still prints as blank. Not even nil, just blank.

It really seems like somehow dataRepresentation... is looking in the wrong place for the data. It always sees it as blank, when it prints fine from any other place in the code, and the whole program works except for saving/opening. If I add an object to one of the arrays and then print it, all in dataRepresentation..., it prints fine. It's like it knows what fields /should/ be there, but is recording the ones from a blank document. Do I need to somehow tell it where to look for the data?

I haven't implemented the C-arrays yet.

 - KH
----
Is it saving *or* loading which fails? i.e. the file written to disk, does it actually contain something?

Or was it to be understood as it actually works, except that the print statement doesn't show the date when printing happens in the dataRepresentation... method?
----
It's hard to say whether loading works or not. Both saving and loading work, in the sense that they execute without crashing, and that when the user uses Open... it opens the desired document in a new window with the name of the saved document in the title bar. However, since saving apparently is saving only blank data, loading only loads the blank stuff, resulting in an empty document when an Open... command is executed.

Save... is writing a file to disk with a 8 KB mass (there's not much to save) and with the proper icon, extension, etc. However, the data that I try to print in the code (see the dataRepresentation... method in MyDocument) comes out as blank.

- KH

----

You are instantiating another instance of MyDocument in the MyDocument.nib. **Don't** do this, because MyDocument is already being instantated as the File's Owner. To fix this, remove the MyDocument instance in MyDocument.nib and link everything up to File's Owner instead. Because there are two documents, there are two sets of data, and the File Owner's (the real document's) data never got set and therefore was always blank when saving. As a side note, you should really consider cleaning up the code and learning some good memory management (release & retain). You should also never call "dealloc" directly on any object except "super". -- RyanBates

----
Saving and loading works just fine now. I could've sworn my books created new instances though.

The dealloc stuff was a typo that I fixed, thanks. As for the memory management, I thought I had cleaned most of that up, since the arrays are set and gotten via setter methods in MatrixData. The C-arrays would probably help so I don't have to reference stuff 12 times each.

BTW, is there a quick and easy way to generate a custom NSView for printing? I tried making a custom View in interface builder that encapsulated the portions of the window I wanted to print and then making it outlet to the MyDocument, but something's up with the printing. The print panel comes down correctly, and a job is being sent to the printer, but no page is ever printed, I'm assuming because it's getting no data. I'm using

    -(void)printShowingPrintPanel:(BOOL)showPanels

generating a an NSPrintOperation, and then sending it to 

    [self runModalPrintOperation:printOp delegate:nil  didRunSelector:NULL  contextInfo:NULL];
----

This is kind of off topic, so anyone can move it if they want:

That's strange, you'd think a blank page would print. Try clicking "Preview" on the print panel to see if anything's actually on the page. Anyway, here's how I'd handle the printing:

    
// in MyDocument:
- (IBAction)printDocument:(id)sender
{
	NSPrintOperation *printOperation;
	
	printOperation = [NSPrintOperation
			printOperationWithView:[self printableView]
			printInfo:[self printInfo]];
	
	[self runModalPrintOperation:printOperation
			delegate:nil
			didRunSelector:NULL
			contextInfo:NULL];
}

- (NSView *)printableView
{
	return self windowForSheet] contentView];
}


This will simply print the contents of the main window (which you may or may not want). If you don't want to print the button on the main window, just throw everything except the button into a super view (choose File > Make Subviews Of > Custom View after selecting everything except the button) and print that view. From what it sounds like, this is may be what you are already doing. Also, don't forget to define the printableView method in the header file of [[MyDocument. -- RyanBates

----
The **Make Subviews Of** did the trick, and that brings me to v1.0 of the thing. Whew. Thanks a lot for all your help, guys. Couldn't have done it without you.

- Kenneth Hoffmann

