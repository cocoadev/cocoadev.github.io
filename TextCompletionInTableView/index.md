---
layout: page
title: TextCompletionInTableView
---



I'd like to set the text completions in a column of a table view. What I've seen about text completion implies that I need to subclass General/NSTextView, and General/NSTableView isn't a subclass of General/NSTextView and doesn't seem to have a way of obtaining its textView either.  

I've tried using an General/NSFormatter, but I haven't even been able to make that extend what the user has typed into one of the valid strings I want. (The application is a bug tracker; I'd like to have a column for status entries that could be "Open", "Fixed", "Not reproducible", etc.)   

Hitting "escape" in the table does bring up a LONG list of default completions, so the basic functionality is there, I just need to figure out a way to set the completion list.

Thanks,

Norm

----
See General/FieldEditor. The executive summary is that editing in tables is handled by a special instance of General/NSTextView with the cell set as its delegate, so you can just subclass that and use the text completion delegate methods.
----

Fast reply!

I've tried some code based on the examples in General/FieldEditor and General/CustomFieldEditor without any luck so far.

    
// in General/MyDocument.h
- (void)windowControllerDidLoadNib:(General/NSWindowController *) aController {
    [super windowControllerDidLoadNib:aController];
	General/NSLog(@"Mydocument windowControllerDidLoadNib\n");
	fieldEditor = General/[[NSTextView alloc] initWithFrame:General/NSMakeRect(0, 0, 0,0)];
	[fieldEditor setEditable:YES];
	[fieldEditor setFieldEditor:YES];
	[fieldEditor setSelectable:YES];
	[fieldEditor setDelegate:self];
	[fieldEditor retain];
}

// This method functions as I expect
- (id) windowWillReturnFieldEditor:(General/NSWindow *)sender toObject:(id)anObject {
	General/NSLog(@"windowWillReturnFieldEditor called\n");
	if (anObject == tableView) {
		General/NSLog(@"\tIt's a call for the tableView's editor\n");
		return fieldEditor;
	} else {
		return nil;
	}
}

// This one never gets called, and I can't figure out why
- (General/NSArray *) textView:(General/NSTextView *)textView 
		   completions:(General/NSArray *)words 
   forPartialWordRange:(General/NSRange)charRange 
   indexOfSelectedItem:(int *)index {
	//
	General/NSLog(@"Finding completions...\n");
	int iStatus = [tableView columnWithIdentifier:@"Status"];
	// int iPriority = [tableView columnWithIdentifier:@"Priority"];
	General/NSString *string = General/textView string] substringWithRange:charRange];
	[[NSArray *names = nil;
	General/NSMutableArray *completions = General/[NSMutableArray array];
	if ([tableView editedColumn] == iStatus) {
		names = General/[[NSArray alloc] initWithObjects:
			@"Open", 
			@"Not reproducible",
			@"Won't fix",
			@"Postponed",
			@"Duplicate",
			@"By design",
			@"Fixed", 
			nil];
	}
	if (names) {
		int i;
		General/NSRange whereFound;
		for (i = 0; i < [names count]; i++) {
			General/NSString *name = [names objectAtIndex:i];
			whereFound = [name rangeOfString:string options:General/NSCaseInsensitiveSearch];
			if ((whereFound.location == 0) && (whereFound.length > 0)) {
				[completions addObject:name];
			}
		}
	}
	return completions;
}


Any thoughts on what I'm forgetting?
