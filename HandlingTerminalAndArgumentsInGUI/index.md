---
layout: page
title: HandlingTerminalAndArgumentsInGUI
---



I made a wrapper for a command line app, set up to take input from several NSTextFields. Currently, the program gets the necessary strings and appends them to an NSMutableString which in-turn outputs the whole string to a specified NSTextField in the GUI. This is then grabbed from the GUI and passed as an argument to the comand line app. The thing is that the command line app shows this:

commandapp "-argument pathoffile"

What I need to do is pass it like this:

commandapp -argument pathoffile

The string gets passed but with quotes as well. How can I pass the string to the task and lose the quotes?

----

See the docs for NSTask. Particularly the     + (NSTask *)launchedTaskWithLaunchPath:(NSString *)path arguments:(NSArray *)arguments method. Your args should be in an array, not a string.

----

Is there a way that anything typed in the NSTextField can get sent to an NSArray so I don't have to manually change the array? Each argument I have receives different information {NSTextField} so if I had 3 different arguments, I would need 3 different NSArrays. Or better yet, how would you program it to handle multiple arguments instead of one? The only examples I find on the Internet is for 'ls' and they only show it using one argument which is in the NSArray.

*Editor's note: see also the code snippet example on the main NSTask page**

----

    NSArray *argArray = [NSString componentsSeparatedByString:@" "]

It would be a much better interface, though, to have appropriate UI elements for the options - checkboxes for switches, 'Browse...' button with a NSOpenPanel for choosing the path, and so on. Then when the user launches your task, check the state of the UI, and add the args to the array as appropriate. You'll need to use NSMutableArray for that, then     copy it to make it immutable for passing to NSTask.

CocoaDevCentral tutorial on wrapping unix commands with NSTask: [http://cocoadevcentral.com/articles/000025.php] & [http://cocoadevcentral.com/articles/000031.php]

----

A "Browse..." button gets the path and outputs it in the NSTextField. The arguments I placed in a NSComboBox that the user can select from.  The problem is making some sort of 'if' loop because each argument takes different variables depending on what the argument is. Also, I've seen the those tutorials at CocoaDevCentral and they only use one argument,  ls, as an example which is hard-coded into the NSArray.

The way the program works is:

- if -arg1 is selected, you need path specified  in field 1

- if -arg2 is selected, you need path specified  in field 1 and 2

- if -arg is selected, you need path specified in field 3

I don't understand how to code this type of 'if' statement in an NSArray or NSMutableArray. What I was trying to do in my initial post is make an NSMutableString *temp. Append all the needed argument and field(s). Made a hidden NSTextField to output temp to and pass the whole string to the NSArray like ex [fullPath stringValue]. The problem is that the whole thing appears between quotes.

----

I'm guessing you have a single method as the action of some RUN button (or some other IB object). Assemble your argument array inside that method, hardcoding the "if" statement in the method itself (not the array). Example in pseudocode:
    
-(void)runCommand:(id)sender
{
   NSTask *task;
   NSMutableArray *args = [[NSMutableArray alloc] init];
   // Or use a regular array, using initWithObjects: instead of addObject: in the if statement

   if (*radio button 1 is selected*) {
      [args addObject:*value of text field 1*];
   } else if (*radio button 2 is selected*) {
      [args addObject:*value of text field 1*];
      [args addObject:*value of text field 2*];
   } else {
      [args addObject:*value of text field 3*];
   }

   task = [NSTask launchedTaskWithLaunchPath:*@"/path/to/executable"* arguments:args];
}

Hope that helps, and I also hope I didn't screw anything up in my example. --JediKnil

----

Thank you very much! That was exactly what I needed. :)

