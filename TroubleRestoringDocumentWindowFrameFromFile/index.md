---
layout: page
title: TroubleRestoringDocumentWindowFrameFromFile
---

Trouble Restoring Document Window Frame From File

This seems like a good place to start with for my topic. I am modifying Garfinkel-and-Mahoney's example document-based application to sport an General/NSTableView instead of an General/NSTextView as the mainstay of the document interface. Their example subclasses General/NSWindowController, and I go along with that. So far, I have induced the document to save to a file and to reload the table data overriding the usual methods in the General/NSDocument subclass along with additional accessors in the document subclass and support in the controller. However a weird thing is happening when I try to load a previously saved document, which includes data for the screen location of the document window when it was saved. There is a transient flash of a window that opens at the right location, and then another window opens with the table data, but at a location as if the window was created with the New command (that is, it opens at the default location, including the way new window locations cascade down and to the right across the screen). I have checked in the debugger and when the data are unarchived from disk, the saved window location (as an General/NSRect structure) is available, but somehow the window created for the Open command doesn't stick. Somehow the way I am setting things up (a copy of the instance variables for the window open on screen and a separate copy to preserve in the disk file) is not working properly. The only instance variable I preserve are the array of General/NSDictionary objects that hold the table data, and the General/NSRect representing the frame returned by [ [ self window ] frame ] as follows:
    
// Method used when we archive the data in memory to a data object saved in a 
// disk file (in the controller class)

- ( void ) synchronizeData
{
	General/TabularDoc *doc = [ self document ];
	
	// Attempt to copy the relevant instance variables into a document that 
        // can be stored on disk
	
	[ doc setTblArray: records ];
	[ doc setFrame: [ [ self window ] frame ] ];
}

 which is invoked from the document class
    
- ( General/NSData * ) dataRepresentationOfType: ( General/NSString * ) aType
{
	// This code writes the document from the given data
	if ( [ aType isEqualToString: @"General/TabularDoc" ] )
	{
		// Ask our open windows to dump their current data for storage
		[ [ self windowControllers ]
			makeObjectsPerformSelector: @selector( synchronizeData ) ];
		
		// Archive the data that we just encoded in encodeWithCoder
		return [ General/NSArchiver archivedDataWithRootObject: self ];
	}
	
	return nil;		// In case we were not able to encode the data
}

Apparently this approach succeeds as far as it goes because the desired data are present when we reverse the process and open the file:
    
- ( BOOL ) loadDataRepresentation: ( General/NSData * ) newDocData ofType: ( General/NSString * ) aType
{
	// This code reads the document from the given data
	if ( [ aType isEqualToString: @"General/TabularDoc" ] )
	{
		General/TabularDoc *temp;
		
		temp = [ General/NSUnarchiver unarchiveObjectWithData: newDocData ];
		if ( temp )
		{
                        // inspection of "self's" frame at this point shows the right data
                        [ self setFrame: [ temp frame ] ]; 
			[ tableArray setArray: [ temp tblArray ] ];
			return YES;
		}
	}
	
	return NO;
}

I will include my code for the windowDidLoad method in the controller, which must also play a role in my problem:
    
- ( void ) windowDidLoad
{
	General/TabularDoc *doc;
	
	[ super windowDidLoad ];
	
	doc = [ self document ];
	
	// Set up the array from the document class
	[ records setArray: [ doc tblArray ] ];
	
	if ( [ doc hasFrame ] )
	{
              // This is where I try to restore the window position
	      [ [ self window ] setFrameOrigin: [ doc frame ].origin ]; 
	}
	
	[ tableView reloadData ];
}


Thanks in advance to any takers.

----

Hmmm ... this sounds familiar, but I can't quite place it. You might consider turning off cascading for the document if you're going to re-apply a frame from document data. Given some more information (a working project?) I can continue to narrow this down with you ... -- General/MikeTrent

We tracked this down to two problems: 


* The nib file was set to show the window on "Launch" -- which in this context means when the nib file is loaded.
* Someone needs to turn off cascading in the case where a window frame already exists. I suggested doing that in windowDidLoad after we learn the document has a frame.


With those two changes, it worked great. 

-- General/MikeTrent
