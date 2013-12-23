---
layout: page
title: StackTraces
---




There is code to do backtraces under most conditions included in the boinc project and is available under the LGPL.

http://boinc.berkeley.edu/trac/browser/trunk/boinc/lib/mac

It seems to work nicely as far as I can see so far (and it works on 10.4 and possibly 10.3 as well as 10.5)

You need to grab all of the files in the /mac directory and then include mac_backtrace.h in your code. http://goo.gl/OeSCu

The function to call is PrintBacktrace


----

I'm placing this at the top because it's so overwhelmingly superior to previous attempts.  It's time to rejoice if you've ever needed or wanted to capture a call stack at run time in a simple manner.

It looks like 10.5 has trivialized this problem.  It provides three functions specifically designed for this: backtrace, backtrace_symbols, and backtrace_symbols_fd.

The following example demonstrates the new functions, printing out the current call stack.  Fancier, NSObject category additions should be obvious.

Compile with nothing more than gcc EXAMPLE.c  -- John Engelhart

    
#include <stdio.h>
#include <execinfo.h>

void func_one(void);
void func_two(void);
void dump_frames(void);

int main(int argc, char *argv[]) {
  func_one();
}

void func_one(void) {
  func_two();
}

void func_two(void) {
  dump_frames();
}

void dump_frames(void) {
  void *backtraceFrames[128];
  int frameCount = backtrace(&backtraceFrames[0], 128);
  char **frameStrings = backtrace_symbols(&backtraceFrames[0], frameCount);

  if(frameStrings != NULL) {
    int x = 0;
    for(x = 0; x < frameCount; x++) {
      if(frameStrings[x] == NULL) { break; }
      printf("%s\n", frameStrings[x]);
    }
    free(frameStrings);
  }
}


----
-- This does not produce a stack trace containing Obj-C method names. It's rather useless for Obj-C apps.
----

----

It sounds too good to be true... Now try it in a Cocoa program, like they did below and it does not work, the symbol are "absurds"
I've even tried with the new returnStackAddresses API and same thing, the symbols are not correct, while the old technique works perfectly.
Unfortunately, atos is not installed by default with OS X 10.4 nor 10.5 !! this is a crime :)

Developer are left alone in the dark, when Apple could have changed everything by installing atos by default.
BTW: Don't try to embed atos in your app, it does not work :)

----

From a mailing list post by Jesse Grosjean:

I'm sure things can be made much nicer, but i'm happy to report that 
i've got stack traces working without needing to run under GDB. I'll fix 
things up so that the formatting is nicer, and code cleaner. If someone 
more knowledgeable notices anything dumb (non cosmetic) that i'm doing 
please let me know.  The code depends on /usr/bin/atos being located on 
your machine...wonder if that's standard for OSX or added by the 
developer tools? (/usr/bin/atos is found in the BSD package, which is part of the standard install, with the caveat that a user can de-select it when installing Mac OS X.)

You can print a stack trace in your program by:

1. Importing the ExceptionHandling.framework and enabling logging of 
exceptions.
    
#import <ExceptionHandling/NSExceptionHandler.h>
  [[NSExceptionHandler defaultExceptionHandler] 
setExceptionHandlingMask:NSLogAndHandleEveryExceptionMask];


2.  Adding a category to NSException with the method printStackTrace.
    
- (void)printStackTrace {
     NSString *stack = self userInfo] objectForKey:[[NSStackTraceKey];
     NSTask *ls=[[NSTask alloc] init];
     NSString *pid = [[NSNumber numberWithInt:getpid()] stringValue];
     NSMutableArray *args = [NSMutableArray arrayWithCapacity:20];

     [args addObject:@"-p"];
     [args addObject:pid];
     [args addObjectsFromArray:[stack componentsSeparatedByString:@"  "]];
     // Note: function addresses are separated by double spaces, not a single space.

     [ls setLaunchPath:@"/usr/bin/atos"];
     [ls setArguments:args];
     [ls launch];

     [ls release];
}

3. To print a stack trace from your program you can do this.
    
NS_DURING
	// throw exception
NS_HANDLER
	[localException printStackTrace];
NS_ENDHANDLER


----

This code doesn't work for me. I get "unable to locate process". This may be because I tried it in a small Foundation hack that just raises an exception and prints its stack trace and then quits. It seems that the process is terminated before the atos process has time to inspect it.

This probably doesn't happen if you use it in a real program, that will continue executing, but I made it work by appending the following line after [ls launch]:

    
[ls waitUntilExit];


(I also changed getpid() to [[NSProcessInfo processInfo] processIdentifier].)

--PeterLindberg

----

On OS 10.4 and later you might try [NSThread callStackSymbols]. According to Apple Docs: "This method returns an array of strings describing the call stack backtrace of the current thread at the moment this method was called. The format of each string is non-negotiable and is determined by the backtrace_symbols() API"

--BarryTolnas

----

I use popen() instead, this allows me to pipe the output of atos through tail (to remove the first few lines, which are identical for all my exceptions), c++filt (which will de-mangle C++ names) and 'cat -n' (which will enumerate the lines, making it a little easier to read). Here is the code (which is in ObjC++):

    
	NSString *str = [NSString stringWithFormat:
		@"/usr/bin/atos -p %d %@ | tail -n +3 | c++filt | cat -n",
		getpid(), localException userInfo] objectForKey:[[NSStackTraceKey]];

	if(FILE *fp = popen([str UTF8String], "r"))
	{
		char resBuf[512];
		while(size_t len = fread(resBuf, 1, sizeof(resBuf), fp))
			fwrite(resBuf, 1, len, stderr);
		pclose(fp);
	}


--AllanOdgaard

----

Let's take this one step farther. We can replace the default exception handler by supplying a couple more categories. Create NSApplication(ESExceptionHandling) and override the following method of NSApplication:

    
- (void)reportException:(NSException *)anException
{
	// handle the exception properly
	(*NSGetUncaughtExceptionHandler())(anException);
}


Add this category to the PCH:

    
//
//  Prefix header for all source files in the 'MyApplication' project
//

	
#ifdef __OBJC__
   	#import <Cocoa/Cocoa.h>
    	#import "NSApplication+ESExceptionHandling.h"
#endif


Define this function in your application delegate class to handle all uncaught exceptions:

    
void CB_exceptionHandler( NSException *exception )
{
	[exception printStackTrace];
    
	// exit with an error code... everything is not okay
	exit( ESUnhandledExceptionError );
}


Add the following to your application delegate's -applicationDidFinishLaunching: method:

    
	// use our exception hander for stack trace printout
	NSSetUncaughtExceptionHandler( *CB_exceptionHandler );
    
	// this will give us some better exception handling capabilities
	[[NSExceptionHandler defaultExceptionHandler] setExceptionHandlingMask:( NSHandleUncaughtExceptionMask | NSHandleUncaughtSystemExceptionMask | NSHandleUncaughtRuntimeErrorMask | NSHandleTopLevelExceptionMask | NSHandleOtherExceptionMask )];


BTW, my NSException+ESStackTrace category was modified a little from the previous posted versions. Here it is for reference. It displays all the generic information one could ask for.

    
- (void)printStackTrace
{
    NSString *stackTrace = self userInfo] objectForKey:[[NSStackTraceKey];
    NSString *str = [NSString stringWithFormat:@"/usr/bin/atos -p %d %@ | tail -n +3 | head -n +%d | c++filt | cat -n",
        [[NSProcessInfo processInfo] processIdentifier],
        stackTrace,
        (stackTrace componentsSeparatedByString:@"  "] count] - 4)];
    FILE *file = popen( [str UTF8String], "r" );

    if( file )
    {
        char buffer[512];
        size_t length;

        fprintf( stderr, "An exception of type %s occured.\n%s\n", [[self name] cString], [[self reason] cString] );
        fprintf( stderr, "Stack trace:\n" );
        
        while( length = fread( buffer, 1, sizeof( buffer ), file ) )
        {
            fwrite( buffer, 1, length, stderr );
        }
        
        pclose( file );
    }
}


- Eliot Simcoe

----

Much thanks for all the above suggestions, particularly Eliot's.

One improvement i might suggest for Eliot's idea is to put the fourth block of code (the one that sets the uncaught exception handler and the exception handling mask) in you application delegate's **-applicationWillFinishLaunching** rather than -applicationDidFinishLaunching.

Since the stack trace key is apparently not generated until after the exception handling mask has been set, the above code will fail for any exceptions thrown during application launch (for me this was causing an infinite recursion since the unknown stack trace key would cause an exception to be thrown inside the handler).

- emory

----

Is it possible to adapt this so that one can get a picture of the stack in real time? One reason might be that you have a method that mostly is called by "self" or "super" but might get a message from elsewhere. If elsewhere, you might like to [[NSLog this fact. Right now, I can see no other way to find out WHO sent you a message other than walking the stack to see who is waiting for the reply...

David

----

Normally NSApplication will catch unhandled exceptions that occur in the main thread, print them and continue.  The code above will cause your program to exit after printing the stack trace.  If you do the following instead, then it will print the trace and keep running as normal.

    
- (void)reportException:(NSException *)anException
{
	[anException printStackTrace];
}


-JoshMinor

----
Any chance of getting full listings on these stack trace solutions? I'm not exactly sure where each of these blocks of code belongs. If I get this to work independently I will post the code myself.

-AlainODea

----
FYI, don't put the -setExceptionHandlingMask: call in the -awakeFromNib method. You wont get any stack traces (although your custom exception handler will work fine).
-MichaelBianco 
----


.

.

.


I'm putting this way down here because it's not a particularly wise idea, but hey... it works. :)

If you link to the private Symbolication.framework you can do....

    
struct _VMURange {
    unsigned long long location;
    unsigned long long length;
};



@interface VMUAddressRange : NSObject {
    struct _VMURange _addressRange;
}

@end



@interface VMUSymbol : VMUAddressRange {
    NSString *_name;
    NSString *_mangledName;
    void *_owner;
    unsigned int _flags;
}

- (id)name;

@end



@interface VMUSymbolicator : NSObject {
    NSMutableArray *_symbolOwners;
    NSArray *_symbolOwnerAddressRanges;
    NSString *_path;
    void *_machTaskContainer;
    BOOL _isProtected;
}

+ (id)symbolicatorForPid:(int)fp8;
- (id)symbolForAddress:(unsigned long long)address;

@end



@implementation Controller

void callAFunction()
{
	VMUSymbolicator * symbolicator = [VMUSymbolicator symbolicatorForPid:getpid()];
	NSArray * addresses = [NSThread callStackReturnAddresses];
	
	for (NSNumber * address in addresses) {
		VMUSymbol * symbol = [symbolicator symbolForAddress:[address unsignedLongLongValue]];
		NSLog(@"%@", [symbol name]);
	}
	
}

- (IBAction)test:(id)sender;
{
	callAFunction();
}

@end




This will actually print a real stack trace just like CrashReporter... Actual result:

    
callAFunction
-[Controller test:]
-[NSApplication sendAction:to:from:]
-[NSControl sendAction:to:]
-[NSCell _sendActionFrom:]
-[NSCell trackMouse:inRect:ofView:untilMouseUp:]
-[NSButtonCell trackMouse:inRect:ofView:untilMouseUp:]
-[NSControl mouseDown:]
-[NSWindow sendEvent:]
-[NSApplication sendEvent:]


----

After working with the examples above, I made two changes for Mac OS X 10.6

(1) The printStackTrace method shown requires the UNIX atos tool which most users won't have unless they install the Mac OS X developer tools package.  In 10.6, you can work around this by using callStackSymbols as shown below.

    
- (void)printStackTrace
{
	if( [self respondsToSelector: @selector(callStackSymbols)] ) {
		NSArray *callStackSymbols = [self callStackSymbols];
		NSString *symbol;
		int count, i;
		count = [callStackSymbols count];
		for (i=0; i<count; i++) {
			symbol = [callStackSymbols objectAtIndex:i];
			NSLog(@"%@",symbol);
		}
	}
	else {
		NSString *stackTrace = self userInfo] objectForKey:[[NSStackTraceKey];
		NSString *str = [NSString stringWithFormat:@"/usr/bin/atos -p %d %@ | tail -n +3 | head -n +%d | c++filt | cat -n",
			[[NSProcessInfo processInfo] processIdentifier],
			stackTrace,
			(stackTrace componentsSeparatedByString:@"  "] count] - 4)];
		FILE *file = popen( [str UTF8String], "r" );

		if( file )
		{
			char buffer[512];
			size_t length;

			fprintf( stderr, "An exception of type %s occured.\n%s\n",
				[[self name] cStringUsingEncoding:NSUTF8StringEncoding],
				[[self reason] cStringUsingEncoding:NSUTF8StringEncoding] );
			fprintf( stderr, "Stack trace:\n" );
			
			while( length = fread( buffer, 1, sizeof( buffer ), file ) )
			{
				fwrite( buffer, 1, length, stderr );
			}
			
			pclose( file );
		}
	}
}


(2) Eliot Simcoe's exception handler exits the program.  Once a program is in distribution, you may prefer to continue normal execution after logging an uncaught exception.

    
void CB_exceptionHandler( [[NSException *exception )
{
	@try{
		[exception printStackTrace];
	}
	@catch( NSException *theException ) {
		NSLog(@"Exception during printStackTrace");
	} 	

	// restart runloop
	[NSApp run];

}


- Peter Sichel - Sustainable Softworks


----

Is it possible to programmatically obtain the stack trace for all threads, similar to what you get when you hit a break point in the debugger? Thanks

