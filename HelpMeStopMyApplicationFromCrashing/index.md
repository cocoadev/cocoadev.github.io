---
layout: page
title: HelpMeStopMyApplicationFromCrashing
---



I am currently writing a Prefs Pane for some tools that need to run as root. Adhering to ADC's suggestions, I am using a separate 'helper' tool, designed to be used to perform the privileged operations.

I've included the function in question below in a very stripped down version that eliminates lots of code to be posted, but still produces exactly the same crash.

    

- (BOOL)findTool {

	NSBundle *thisBundle = [[NSBundle alloc] init];
	NSString *helperResource = [[NSString alloc] init];
	char toolPath[MAXPATHLEN];
	
	if(thisBundle = [NSBundle mainBundle]){
		NSLog(@"thisBundle is: %@",thisBundle);
		
		if(helperResource = [thisBundle pathForResource:@"Helper" ofType:NULL]){
			
			NSLog(@"helperResource is: %@",helperResource);
			
			if([helperResource getFileSystemRepresentation:toolPath maxLength:MAXPATHLEN]){
				NSLog(@"toolPath is: %s",toolPath);
			}else{}
		}else{}
	}else{}
	
	// Release the variables
	[thisBundle release];
	[helperResource release];
	
	return TRUE;
}



However, when using some of Cocoa's objects (namely NSBundle and NSString), my program is crashing. I've narrowed it down to one particular line:

    if ( helperResource = [thisBundle pathForResource:@"Helper" ofType:NULL] )

If I comment out the line: 

     if([helperResource getFileSystemRepresentation:toolPath maxLength:MAXPATHLEN]){  (along with the matching      }else{} ) 

the program does not crash.

Below is some NSLog output:

    
[Session started at 2006-01-26 14:49:21 -0600.]
2006-01-26 14:49:24.104 TestProg[20109] thisBundle is: NSBundle </Users/da5id/TestProg/build/Debug/TestProg.app> (loaded)
2006-01-26 14:49:24.104 TestProg[20109] helperResource is: /Users/da5id/TestProg/build/Debug/TestProg.app/Contents/Resources/Helper
2006-01-26 14:49:24.104 TestProg[20109] toolPath is: /Users/da5id/TestProg/build/Debug/TestProg.app/Contents/Resources/Helper


Ed.: [snip] -- doubt that we need the big huge crash log any more

- Shane

----
There are a lot of issues with that code. You're allocating an NSBundle object and an NSString object, but never releasing them; instead, you're releasing [NSBundle mainBundle] and [thisBundle pathForResource:@"Helper" ofType:NULL], which are themselves autoreleased already, and never retained. Also, you assign to these variables in your if statements, which is IMHO bad form, and will raise warnings in GCC because it's usually a typo (= instead of ==). You might want to brush up on MemoryManagement in Cocoa and ObjectiveC.

Try the following:
    
- (BOOL)findTool {
	NSString *helperResource;
	char *toolPath;
	
	helperResource = [[NSBundle mainBundle] pathForResource:@"Helper" ofType:NULL];
	if(helperResource){
		NSLog(@"helperResource is: %@",helperResource);

		/* You don't need to free(toolPath) because its lifetime is the
		same as the (autoreleased) object pointed to by helperResource. */
		toolPath = [helperResource fileSystemRepresentation];
		NSLog(@"toolPath is: %s",toolPath);

		/* do stuff with toolPath here */

		return YES;
	}else{
		NSLog(@"Helper resource is missing.");
		return NO;
	}
}


*Edit: fixed a stray [[NSString alloc] init] in helperResource's initializer.*

- JonathanGrynspan

----

Thank you very much for your help.  It works wonders now!  I will most certainly be taking deeper looks into MemoryManagement.  It has been many years since I've worked with C, and this is my first time giving ObjC a real shot.  I have a lot to learn, but I certainly don't mind doing the research.  Thanks for pointing me in the right direction!

----


Hrm... maybe you could help me with this one too...

I get an      EXC_BAD_ACCESS / KERN_PROTECTION_FAILURE  when I run the following function.


    
- (BOOL)findHelperTool:(NSString *)toolName ofType:(NSString *)toolType setPath:(char *)toolPath {
	NSBundle *thisBundle = [NSBundle bundleForClass:[self class]];
	NSString *helperResource = [thisBundle pathForResource:toolName ofType:toolType];
	
	DEBUG(fprintf(stderr, "helperResource='%s'\n",[helperResource UTF8String]));

	if(helperResource){
		strcpy(toolPath,[helperResource fileSystemRepresentation]);
		return YES;
	}else{
		return NO;
	}
}



The function is called elsewhere using the following code:

    
	char helperPath[MAXPATHLEN];
	BOOL foundTool = NO;

	foundTool = [self findHelperTool:@"Helper" ofType:NULL setPath:helperPath]


The function actually finds the 'Helper' tool, but crashes somewhere afterwards, and I have no idea why.  The DEBUG macro prints out the proper pathname for the resource, but crashes immediately afterwards.  Seems I'm trying to write data to a read-only area, but I don't know how or why this is happening.  I've changed the code from the code you provided me because this is actually a preference panel, and getting the mainBundle won't work here (as I'm guessing the mainBundle is the System Preferences bundle).

I'm wondering if perhaps these crashes have something to do with the      [NSBundle bundleForClass:[self class]]  functionality, as that seems to be a common denominator for both of the crashing functions.

In the end, this function needs to find the resource, and set the path to the C String argument 'toolPath'.

Thanks for any help any of you could provide.

----
Despite the name, there is *no* guarantee that paths on the system will be less than MAXPATHLEN characters in length. So this is a bad design. Instead, I would recommend keeping the NSString path around, and just invoking     -fileSystemRepresentation whenever you need the C string path.

Oh, and in order for us to better help you, it would be good for you to tell us exactly *where* the code crashes.

----

I don't see what other information I could give you, if you've read the entire post I made prior to yours, you know as much as I do.  Aside from putting up the CrashReporter log, that's all I know.  The program crashes when this particular function is called.  When I take this function OUT, it doesn't crash.  That's all I know.  I don't know why it's crashing, but if I change the      [NSBundle bundleForClass:[self class]]   to be      [NSBundle mainBundle]  it works fine (but doesn't find the Helper tool, because the 'mainBundle' is actually the bundle that houses the System Preferences app.

----
What other information can you give us? Like I said, tell us *where* the code crashes. Make sure debug symbols are turned on, run your application in the debugger, and tell us precisely which line is crashing.

