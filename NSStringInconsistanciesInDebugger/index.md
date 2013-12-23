---
layout: page
title: NSStringInconsistanciesInDebugger
---



Hello,

I have a method in my program that is supposed to check for the number of
e-mail messages on a POP3 server. I am using EDCommon's EDStream class for
the connection to the server. Everything works fine except after I issue the
STAT command to the server to get the number of messages.

Here is the important code:
    
[_popConnection writeLine: @"STAT"];
statResult = [NSString stringWithString: [_popConnection availableLine]];
resultCode = responseRegex substringForSubexpressionAtIndex:1 inString: statResult] autorelease];


So, all seems to work well. As I step through the code in the debugger, the
first line goes through okay, and so does the second one. The list of
variables in PB lists statResult as having a valid response from the server,
and the memory address turns red after the second line executes since the
contents of the variable changed.

Now, here's the problem. Even though statResult is reported by PB as having
valid contents, if I issue 'po statResult' to GDB (from with PB of course),
GDB reports that the string has no contents at all. I don't have a
global/member variable named statResult because all of my member variables
are prefixed with an underscore and I don't have any globals to begin with!

The final line in my code example above doesn't work either. It too seems to
think that statResult contains @"" and therefore returns nil.

Why the heck is Project Builder telling me that this variable has a correct
response from the mail server, but everything else seems to be treating
statResult as though it only contained an empty string? This is the only
method in my program where this happens, and I do have other methods that
issue commands to the server using [[EDStream and parse the response with
MOKit's MORegularExpression. It just doesn't make any sense to me! What am I
doing wrong?

One more thing I should probably add is that if I use NSLog to display the contents of statResult, it also displays its correct contents.

TIA,
MarcWeil

----

Another update, it seems that GDB will always print out an empty string if the NSString you are telling it to print out (with the 'po' command) begins with a plus symbol ("+"). The only workaround I can find is to prefix the string that you know will contain a "+" as the first character with a space.

I have reported this bug with Apple (report # 3255261) through http://developer.apple.com/bugreporter/ so hopefully it will be looked at by them.

Can anybody else reproduce this problem as well? I'm hoping this isn't just a problem with my setup or something. Just make an NSString that is a "+". For example:
    
NSString *plusCharTester = [NSString stringWithString: @"+"];
NSLog(@"There SHOULD be a + character printed out here: %@", plusCharTester);

(Note that the % followed by the @ in the code above shouldn't have a space in-between. Seems to be a bug in the Wiki...)

Just put that into a simple project or something and see if the plus is printed out.

Thanks!
MarcWeil

