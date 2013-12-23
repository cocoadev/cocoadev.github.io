---
layout: page
title: ConvertStringIntoExpression
---



I want the user to enter an expression in a field and then, this expression can be calculated and the result be returned. http://goo.gl/OeSCu

For example, you enter "4 + 5" into a NSTextField, and the stringValue is converted into a real expression that returns 9.

Yet, I have no idea how to do that, except reinvent the wheel...

-- Trax

----

The following example just needs two NSTextFields and a controller object that responds to one action (    inputAction:). This invokes the command-line tool *bc*, at /usr/bin/bc. There is related material at BCWrapperSource, and more at HandlingLargeNumbers.

**ControllerObject.h**

    
@interface ControllerObject : NSObject {

    IBOutlet NSTextField *inputField, *outputField;
    NSTask *bcTask;
    NSFileHandle *inputHandle, *outputHandle;

}
- (IBAction)inputAction:(id)sender;
- (void)bcTaskOutput:(id)note;
@end


**ControllerObject.m**

    
@implementation ControllerObject 

- (void)awakeFromNib {
    [[NSNotificationCenter defaultCenter] addObserver:self
                    selector:@selector(bcTaskOutput:)
                    name:NSFileHandleDataAvailableNotification 
                    object:nil];
}

- (void)dealloc {
    [[NSNotificationCenter defaultCenter] removeObserver:self];
    [super dealloc];
}

- (void)bcTaskOutput:(id)note {
    id object = [note object];
    if (object == outputHandle) {
        NSData *data = [outputHandle availableData];
        NSString *output = [NSString stringWithCString:[data bytes] length:[data length]];
        if (output) [outputField setStringValue:output];
        [bcTask terminate]; [bcTask release]; bcTask = nil;
    }
}

- (IBAction)inputAction:(id)sender {
    NSString *input = [sender stringValue];
    NSString *inputString = [NSString stringWithFormat:@"%@\n", input];
    NSData *inputData = [NSData dataWithBytes:[inputString cString] 
                                        length:[inputString length]];
    if (inputData) {
        if (bcTask) {[bcTask terminate]; [bcTask release]; bcTask = nil;}
        bcTask = [[NSTask alloc] init];
        NSPipe *inputPipe = [NSPipe pipe];
        inputHandle = [inputPipe fileHandleForWriting];
        NSPipe *outputPipe = [NSPipe pipe];
        outputHandle = [outputPipe fileHandleForReading];
        [bcTask setStandardOutput:outputPipe];
        [bcTask setStandardInput:inputPipe];
        [bcTask setLaunchPath:@"/usr/bin/bc"];
        [bcTask launch];
        [inputHandle writeData:inputData];
        [outputHandle waitForDataInBackgroundAndNotify];
    }
}
@end


----

There is an example in BookBuildingCocoaApplications that deals with this kind of thing.

The parsing, unfortunately (or not, depending on your point of view) is done with lex and yacc,
but the back end is integrated into a sort of calculator in a Cocoa text view

But, given the desire not to reinvent the wheel, the wheel is there....

----

This is what bugs me in Cocoa, to do something that simple you gotta write a bunch of code... If maybe the guys at Apple were a little bit smarter they should really try implementing the method Evaluate, which I have seen and used a lot in JavaScript, ActionScript, Lingo and some few others.
It could be something like:

 [aNSTextField evaluateValue:[NSTextField string] as:NSMathematicalExpression];

*
If it's so simple, why not write it yourself? Having written code to parse and evaluate mathematical expressions (with Antlr instead of lex and yacc), I don't see what's simple about it.
*

Or use an interpreted language like JavaScript, ActionScript, Lingo, Perl, Ruby, Python, Tcl, shell script, etc.

----

Actually you do have your "evaluate" method in Cocoa, it comes in the form of NSAppleScript.

----

That is some sexy code up there, thanks to whomever passed it along.  It is also a nice introduction to NSTask and NSPipe.

----

I may be wrong, but shouldn't:

NSString *input = [sender stringValue];

be:
NSString *input = [inputField stringValue];

? Sender would be the button that generated the action method- taking the stringValue would return, well I'm not sure..

----

If there was a button,     [sender stringValue] would return the title of the button ("OK" or whatever). If this is the NSTextField's action (triggered by hitting enter in the text field) sender will be the text field.

----

Ahh. I see. I assumed there would be a button to click. That makes sense too.

----

Goto http://lts.online.fr/dev/java/math.evaluator/ and you will find a good java math evaluator. If you really needed a pure objC version, I don't think it would be a huge issue with porting it to objC from java.  I bet it could be done in a day.

----
There are half a dozen or more ways to interpret and evaluate math expressions that are already present in Mac OS X:
*
Applescript
Tcl
Python
the shell eval program
Ruby
Perl
Java Script (used by Quartz Composer)
There used to be an NSInputMnager sample that did this.
There used to be anNSFormatter example that did this.
*

And the code above is nice, but all it does is this
    
- (IBAction)takeExpressionStringFrom:(id)sender
{
   char     answer[PATH_MAX];
   FILE  *fd = popen("bc -i -q", "r+");
   fputs(sender stringValue] cString], fd);
   fputs("\n", fd);
   if(NULL != fgets(answer, PATH_MAX, fd))
   {
      [sender setStringValue:[[[NSString stringWithCString:answer]];
   }
   else
   {
      [sender setStringValue:@"Error"];
   }
   pclose(fd);
}

Just connect any text field to the -takeExpressionStringFrom: action, and whatever expression is types will be replaced by the result of evaluating the expression.  
*
Try 4+5
Try 3^3
Try sqrt(10.5)
*

----
Because it's *so* much better to write code which will explode and die just because the answer is longer than 1023 characters? The original isn't that much better, what with the assumption that all the data will come in one big chunk, but at least it won't experience mysterious and hard-to-debug crashes when the incoming data exceeds the assumed length.

I would suggest that everybody writing code for this page take a minute to ponder what happens when whatever calculation is entered produces a really huge result. I would also suggest that everybody ponder what happens when the user enters non-ASCII characters. See StringWithCString, then rewrite to use stringWithUTF8String and friends.

*
The code above does NOT explode if the result string is longer than PATH_MAX.  I suggest you read the man page for fgets().
It is true, -cString is deprecated.  -stringWithCString is NOT deprecated.  As it happens, most Unix command line programs only correctly handle 7-bit ASCII which makes using UTF8 strings pretty pointless because character values greater than 127 will not work.  Although interestingly, bc does have an option to support wide characters.
*

The code above has been improved slightly by reporting if an error was detected by fgets().  The contents of *answer* are undefined if fgets() detects an error.  Also, EOF prior to a newline in the input is considered an error by fgets(), but that probably can't happen in this example.http://jamtangankanan.blogspot.com/
http://www.souvenirnikahku.com/
http://xamthonecentral.com/
http://www.jualsextoys.com
http://cupu.web.id
http://seoweblog.net
http://corsva.com
http://yudinet.com/
http://seo.corsva.com
http://seojek.edublogs.org/
http://tasgrosir-brandedmurah.com/
http://upin.blogetery.com/
http://www.symbian-kreatif.co.cc/
http://upin.blog.com/
http://release.pressku.com/
http://cupu.web.id/tablet-android-honeycomb-terbaik-murah/
http://cupu.web.id/hotel-murah-di-yogyakarta-klikhotel-com/
http://www.seoweblog.net/hotel-murah-di-semarang-klikhotel-com/
this is very nice blog. i m impressed. Keep up the great work, its hard to find good ones. I have added to my favorites. Thank You.
http://jasaseomurah.om-onny.com
http://www.om-onny.com
http://kanvaslukis.om-onny.com
http://pasangiklanbaris.om-onny.com
http://hotnews.om-onny.com
http://allabout.om-onny.com
http://tokobunga.om-onny.com
http://sinergibisnis.com
http://niziegaleri.com
http://www.jeparacrafters.com
http://www.wongsukses.com
http://citraindah.biz


----

I'm writing a custom NSFormatter.  I use isPartialStringValid for key parsing.  However, I need to know when the user has committed (i.e., pressed enter, etc) to do final validation (for example, I accept fractions, and need to know the "2/" is invalid vs "2/4").   I would only return an object from getObjectValue after final validation is completed.

----

I would think that this is part of the purpose of     -[NSFormatter getObjectValue:forString:errorDescription:].  If the string cannot be transformed into an object (because it's invalid), return     NO and set the error description string.

