---
layout: page
title: CreatingObjectsFromTextData
---

I'm really new to Cocoa but this is the direction I'm heading so I'd like to kind of start getting my head into this as early as possible.   I have a bunch of data in CSV format (several Bible translations).  Its basically arranged into a four level hiararchy like:


*Version / Translation
*Book
*Chapter
*Entry

I would like to:

a) Make reuseable Obj-C objects out of this data, even if each translation is a diferent object accessed independantly, though a single object would be dandy.

b) be able to run text string searches on the entry (lowest) level of the hierarchy

c) manage large chunks of this data quickly (load quickly even when displaying long or many diferent bodies of text)

d) use  message to request data in a range such as:
     [myDataObject translation:1 fromEntry:1,1,1 toEntry:1,4,6]
   which would result in returning all the entries from translation 1 : book 1 chapter 1 entry 1.. to book 1 chapter 4 entry 6.

So I'd like to know first how to objectify this data.  Is there some kind of an import process that makes it easy?  Also, is there a good way to model & create the structure for my objects?  So far all I can think of is using a dictionary to hold all the document names, and allow the dictionary to hold multidimensional array objects for chapter, entry.  Then I'd have to create a separate object for each translation.  This seems pretty inefficient & clumsy and I dont know how to make it searchable.

Maybe this is beyond what I can do now, but I'd like to start working towards it.

Thanks!
CliffPruitt

----

You may want to check out this page if you stumbled across this looking for a way to deal with CSV and TSV files: ReadWriteCSVAndTSV

ChrisMurphy

----

It sounds like what you really want is to load your data into a relational database and query it programmatically. I think trying to do this through Cocoa might be a disaster.

JeffHarrell

----

Entirely off the top of my head, since you'll be dealing with rather large amounts of data, you'll want to pre-process the text into a more useful form which you'll then access at run-time.  I don't think you'll want to parse a couple of Bibles on app launch.  With that being said, you can have all of the text as one big blob in a file.  mmap() it so you can treat it just like it's memory, and the OS will page in and page out stuff as you need (since random access of things will be pretty sparse)  That'll make (b) pretty fast, espeically if you use msync to invalidate regions you've visited but won't come back to.  That also makes (c) do-able. Create a bunch of indexes to indicate where chapters and verses start and end.  Arrays of these would be sufficient since you'll be able to access them numerically.  That'll make (d) possible.  

I'd first do things procedurally to get this stuff basically working, and the see how it comes together, and then refactor and objectify it.  You could do (b) also by building a reverse-map of the text (make a concordance) - for each word, have a list of locations where they live.  Disk is plentiful these days, so you won't necessarily have to have really high powered algorithms here.  ++MarkDalrymple

----

After wandering around the 'net for what seemed like an age, I decided to roll my own.. below is a CSV parser, which
also handles quoted lines correctly e.g. one,two,"three, four",five (Excel tends to export this way). I've left the 
debugging in, it should be fairly obvious as to how it works.. will revise this when I get some spare time. Enjoy! [JayFenton]

    

	// Character delimiter sets
	NSCharacterSet *lineCharacterSet = [NSCharacterSet characterSetWithCharactersInString:@"\r\n"];
	NSCharacterSet *fieldCharacterSet = [NSCharacterSet characterSetWithCharactersInString:@","];
	NSCharacterSet *quotedCharacterSet = [NSCharacterSet characterSetWithCharactersInString:@"\""];
	
	// Import the file as a single string
	NSString *csvData = [[NSString alloc] initWithContentsOfFile:@"filename.csv"];

	// Temporary line and field holders
        // The scanner will alloc and init this variables as needed
	NSString *line;
	NSString *field;
 
		
	NSScanner *mainScanner = [NSScanner scannerWithString:csvData];
	while(![mainScanner isAtEnd]) {

		[mainScanner scanUpToCharactersFromSet:lineCharacterSet intoString:&line];
				
		NSLog(@"FROM %@\n", line, [mainScanner scanLocation]);

		NSScanner *fieldScanner = [NSScanner scannerWithString:line];
		while(![fieldScanner isAtEnd]) {
		
			[fieldScanner scanUpToCharactersFromSet:fieldCharacterSet intoString:&field];
			
			if([field hasPrefix:@"\""] == YES && [field hasSuffix:@"\""] != YES) {

				// No trailing speech mark, go back and take the token all the way up to the next " (ignoring commas)
				NSString *restOfQuotedString = [[NSString alloc] init];
				[fieldScanner scanUpToCharactersFromSet:quotedCharacterSet intoString:&restOfQuotedString];
				
				NSMutableString *newField = [NSMutableString stringWithString:[field stringByTrimmingCharactersInSet:quotedCharacterSet]];
				[newField appendString:restOfQuotedString];

				[fieldScanner scanCharactersFromSet:quotedCharacterSet intoString:nil];
                                [fieldScanner scanCharactersFromSet:fieldCharacterSet intoString:nil];
				
				NSLog(@"TO QUOTED TOKEN %@ (%d)\n", newField, [fieldScanner scanLocation]);

			} else {
				
				[fieldScanner scanCharactersFromSet:fieldCharacterSet intoString:nil];
				NSLog(@"TO TOKEN %@ (%d)\n", field, [fieldScanner scanLocation]);
			
			}
			
		}

	}



----

There's also CSVImporter, an RFC4180 compliant CSV parser implementation to show you the basics of writing a recursive descent parser for importing data into your Cocoa applications.

http://cocoawithlove.com/2009/11/writing-parser-using-nsscanner-csv.html

http://projectswithlove.com/projects/index.html

