---
layout: page
title: NSTaskNSPipeEndOfFile
---


I am trying to write a wrapper for a command line tool that requires user input after launch. I have set up the pipes to and from the NSTask object.
My problem is that when it asks for the first input I can pass that no problem but when it requests the next it immediately sends the text "Uncaught Exception End_of_File" to the output pipe and exits. 
My thinking was that the "close file" statement in my code (below) could be the cause (i.e. its attempting to read input from the closed file handle and getting the end of file) however, as far as i can fathom, this is required to trigger the end of the input.

Does anyone have any pointers?

Here is the code (was intended as a side to get it working before incorporating it into my main program so my apologies for its roughness!)

     

- (IBAction)run:(id)sender
{
	//clean up the view
	[outputText setString:@"Process Started\n"];
	
	//set our attributes
	attributes = [[NSArray alloc] initWithObjects:@"/usr/bin/Unison",[commandText stringValue],[commandText2 stringValue],@"-dumbtty",nil];
	
	//sort out the task
	unisonTask =[[NSTask alloc] init];
	
	//set outputs
	[unisonTask setStandardOutput: [NSPipe pipe]];
	[unisonTask setStandardError: [unisonTask standardOutput]];
	
	//set inputs
	[unisonTask setStandardInput:[NSPipe pipe]];
	
	[unisonTask setLaunchPath:[attributes objectAtIndex:0]];
	[unisonTask setArguments:[attributes subarrayWithRange: NSMakeRange(1,([attributes count] - 1))]];
	
	//register output observer
	[[NSNotificationCenter defaultCenter] addObserver:self 
											 selector:@selector(getData:) 
												 name:NSFileHandleReadCompletionNotification 
											   object:unisonTask standardOutput] fileHandleForReading;
	
	[unisonTask standardOutput] fileHandleForReading] readInBackgroundAndNotify];
	
	//launch it!
	[unisonTask launch];
}

- ([[IBAction)sendCommand:(id)sender
{
	NSLog(@"sending command: %@",[respondText stringValue]);
	[unisonTask standardInput] fileHandleForWriting] writeData:[[respondText stringValue] dataUsingEncoding:NSUTF8StringEncoding;
	
	[unisonTask standardInput] fileHandleForWriting] closeFile];
	

}

- (void) getData: ([[NSNotification *)aNotification
{
    NSData *data = aNotification userInfo] objectForKey:[[NSFileHandleNotificationDataItem];
    // If the length of the data is zero, then the task is basically over - there is nothing
    // more to get from the handle so we may as well shut down.
    if ([data length])
    {
        // Send the data on to the controller; we can't just use +stringWithUTF8String: here
        // because -[data bytes] is not necessarily a properly terminated string.
        // -initWithData:encoding: on the other hand checks -[data length]
        outputText textStorage] appendAttributedString:[[[[[NSAttributedString alloc] initWithString:[[[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding] autorelease]]autorelease]];
    } 
	
	else 
	{
        // We're finished here
        [self stopProcess];
    }
    
    // we need to schedule the file handle go read more data in the background again.
    aNotification object] readInBackgroundAndNotify];  
}

- (void) stopProcess
{
    [[NSData *data;
    
    // It is important to clean up after ourselves so that we don't leave potentially deallocated
    // objects as observers in the notification center; this can lead to crashes.
    [[NSNotificationCenter defaultCenter] removeObserver:self name:NSFileHandleReadCompletionNotification object: unisonTask standardOutput] fileHandleForReading;
    
    // Make sure the task has actually stopped!
    [unisonTask terminate];
	
	while ((data = [unisonTask standardOutput] fileHandleForReading] availableData]) && [data length])
	{
		[[outputText textStorage] appendAttributedString:[[[[[NSAttributedString alloc] initWithString:[[[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding] autorelease]]autorelease]];
	}
	
}

Any help much appreciated! -DG



----
When you close the standard input you can no longer write to it. That's what close means.

If Unison is looking for a return character to delimit input, then you should probably add one. Currently you're writing a line but no return character, so it has no idea that you're done sending it input. When you close the file it's obvious, but this prevents you from re-using the task.

One other note: you read pieces of incoming data and convert them to UTF-8 before appending them to your output. This is dangerous. The way UTF-8 works, it's possible for a slice of valid UTF-8 data to not be valid UTF-8. To be completely safe, you should buffer the incoming data (in an NSMutableData, for example) and then only break it into pieces where you see a known safe delimeter such as a     '\n' in the data. Another possibility is to write a parser that knows enough about UTF-8 to pick out safe character boundaries anywhere, it's not that hard, but you shouldn't need it if your task produces a lot of reasonable-length lines as most UNIX tools tend to do.

----

OK, as i thought it then, i had been trying to just send the return character, but it had not been working. Turns out that the UNIX tool was attempting to  modify the terminal environment to operate with single charicter input without a return character required. Operating the tool in "dumb" mode solved this one! 

Thanks also for your other input. Much appreciated! - DG

