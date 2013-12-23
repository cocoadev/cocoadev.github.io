---
layout: page
title: NSStringHowToInsertOtherNSStringInQuotes
---



I know this is SUCH a lame question, but I'm having a hard time with using %@ inside of a pair of \" escapes.  I'm just on the ground floor of the learning process, but I'm starting to get past the book tutorials stage & start playing with some things on my own.  I'm a huge fan of AppleScriptStudio and so I was immediately interested in NSAppleScript.  I wanted to make a small app that did nothing more than reveal itself in the Finder (via AppleScript) when a button was pressed. To do so the path to the app needs to be enclosed in a pair of quotes that are part of the string. I acomplished that, but had to use the stringByAppendingString method to build the script source in three lines.  I thought I could just insert the variable holding the path using %@.  Is that not the case?

I'll warn everyone, I'm sure there are stupid mistakes in this. Like I said I'm all of two three days into being able to kind of do my own thing.

Thanks so much for any help you can give. Its great to finally be catching on a little. :-)

    
<code>
- (IBAction)runScript:(id)sender
{
    NSString *scriptText, *scriptError, *scriptErrorKey, *pathToApp;
    NSAppleScript *scriptToRun;

    pathToApp = [[NSBundle mainBundle] bundlePath];
    NSLog(pathToApp);
    
    
    //	    Shouldn't the below line work?  It doesn't.  It actually crashes the app.
    //scriptText = (@"Tell Application \"Finder\" \n reveal ((posix file \"%@\") as alias) \n end tell", scriptText);

    //	    I had to use this instead
    scriptText = @"Tell Application \"Finder\" \n reveal ((posix file \"";
    scriptText = [scriptText stringByAppendingString: pathToApp];
    scriptText = [scriptText stringByAppendingString:@"\") as alias) \n end tell"];
    
    NSLog(scriptText);
    scriptToRun = [[NSAppleScript alloc] initWithSource:scriptText];
    [scriptToRun executeAndReturnError:nil];
    
}
</code>


How about using:
    
scriptText = [NSString stringWithFormat: @"message \"%@\" message", scriptText]; 

----
Let's examine the crashing line:
    
    scriptText = (@"Tell Application \"Finder\" \n reveal ((posix file \"%@\") as alias) \n end tell", scriptText);

What happens when you have     (a, b)? This is using the little-known comma operator in C. The comma operator evaluates both sides, and then gives the result of the second. So, for example, writing     int x = (1, 2); is identical to writing     int x = 2;. Writing     int x = (printf("hello world"), 2); will assign 2 to x and also print "hello world".

In the code above, the left side of the comma has no side effects, so it does nothing. Thus, it's equivalent to writing:
    
    scriptText = scriptText;

And of course this does nothing. Since scriptText is uninitialized, your program crashes when it tries to use it.

----

The upshot is that you wanted the following:

    
scriptText = [NSString stringWithFormat:@"Tell Application \"Finder\" \n reveal ((posix file \"%@\") as alias) \n end tell", pathToApp];


You've made two separate errors in your code: not actually calling     -stringWithFormat:; and passing the wrong argument also.

----

Also, an easier way to reveal a file in the Finder is to use the -selectFile:inFileViewerRootedAtPath: in the NSWorkspace class. You can find documentation in http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSWorkspace.html. In your example, you'd use this rather than NSAppleScript:

    
[[NSWorkspace sharedWorkspace] selectFile:pathToApp inFileViewerRootedAtPath:@""];


Easier, isn't it?

*Not only easier, but it won't explode if the path to your application has a " in it.*

----

OK just a slight clarification. I had removed the offending line before posting so I had to retype it for the example and I mistyped one variable name.  You're right, it should have been:

    
scriptText = (@"Tell Application \"Finder\" \n reveal ((posix file \"%@\") as alias) \n end tell", pathToApp);


That was an error in posting here on the site but fortunately I wasn't quite THAT off in my original code. :-)

That said, I was confused about inserting tokens into a string. In the book I'm using (Cocoa Programming for Mac OS X: First edition) there is an example near the beginning that has you use     Log("array : %@", array) to log an NSArray object.  I guess I have two questions there.

1. Does     Log() use     [NSString stringWithFormat:]?
2. Why does the comma operator not have the same effect in this case as stated above?  Is it just not the same context?

I guess I thought you could still use tokens whenever you were working with scrings but I guess that's not the case.

So long story short, I switched to the below code which executes fine.

    
 NSString *scriptText, *scriptError, *scriptErrorKey, *pathToApp;
    NSAppleScript *scriptToRun;

    pathToApp = [[NSBundle mainBundle] bundlePath];
    NSLog(pathToApp);

    scriptText = [NSString stringWithFormat:@"Tell Application \"Finder\" \n reveal ((posix file \"%@\") as alias) \n end tell", pathToApp];

    NSLog(scriptText);
    scriptToRun = [[NSAppleScript alloc] initWithSource:scriptText];
    [scriptToRun executeAndReturnError:nil];


Thanks to all of you guys for the help. I was starting to feel like I was learning in a bubble. :-)


----

    Log("array : %@", array) calls a function,     Log, taking a variable number of arguments; by examining the first string, it determines that it has one extra parameter,     array. This does *not* create a new string and pass it into     Log.

Similarly, [NSString stringWithFormat:@"array : %@", array] sends a message taking a variable number of arguments and returning an NSString.

(@"array : %@", array) is a special case: because it is not prefixed by a function name, it invokes the aforementioned comma operator, which simply discards the first argument. This is what makes lines like     for (i = 0; i < end; i++, x++) work.

----

Starting to make some sense.  I'm sure it'll come the more I'm exposed to Obj-C. Thanks for helping clear up the string confusion.  Its been a huge help.
CliffPruitt

