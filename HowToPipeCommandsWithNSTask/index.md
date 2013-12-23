---
layout: page
title: HowToPipeCommandsWithNSTask
---



How can I use NSTask for a command with a pipe?

For Example
netstat -rn | grep default

I tried this code:
    
NSTask *proc;
	NSPipe *output;
	NSData *data;
	NSString *buffer;
	
	proc = [[NSTask alloc] init];
	output = [[NSPipe alloc] init];
	
	[proc setLaunchPath:@"/usr/sbin/netstat"];
	[proc setArguments:[NSArray arrayWithObjects: @"-rn", @"|", @"/usr/bin/grep", @"default", nil]];
	[proc setStandardOutput:output];
	[proc launch];
	
	data = output fileHandleForReading] readDataToEndOfFile];
	buffer = [[[[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];

...but that does not work. 
Does someone have a hint for me ?

The aim is, to read the default route - of you know an easy way to get that in cocoa, please tell me. --Ben

----
Can you be more specific about "does not work"? Does it crash, freeze, give you nil, what?

----
The Problem: Output from netstat -rn is not piped through grep

----
There is no shell in your example. You're doing the equivalent of this command:

    
/usr/sbin/netstat -rn "|" /usr/bin/grep default


To do what you need, you have several choices. One, you could use     /bin/sh as the command, with     -c as one argument, and the entire command as the second argument. Two, you could create two NSTask<nowiki/>s, one for     netstat and one for     grep, hooking them together with your own NSPipe. Three, you could only execute     netstat, then do the search for     "default" in your own code.

----

I did this:
    
[proc setLaunchPath:@"/bin/bash"];
[proc setArguments:[NSArray arrayWithObjects: @"-c", @"\"/usr/sbin/netstat -rn | /usr/bin/grep default\"", nil]];


the Result is:
    
/bin/bash: line 1: /usr/sbin/netstat -rn | /usr/bin/grep default: No such file or directory


And I did this: 
    
[proc setLaunchPath:@"/bin/bash"];
[proc setArguments:[NSArray arrayWithObjects: @"-c \"/usr/sbin/netstat -rn | /usr/bin/grep default\"", nil]];


with this Result:
    
/bin/bash: - : invalid option
Usage:	/bin/bash [GNU long option] [option] ...
	/bin/bash [GNU long option] [option] script-file ...
GNU long options:
	--debug
	--dump-po-strings
	--dump-strings
	--help
	--init-file
	--login
	--noediting
	--noprofile
	--norc
	--posix
	--rcfile
	--restricted
	--verbose
	--version
	--wordexp
Shell options:
	-irsD or -c command or -O shopt_option		(invocation only)
	-abefhkmnptuvxBCHP or -o option


What the hell happens here ?
If I open a terminal and type in this:
    
/bin/bash -c "/usr/sbin/netstat -rn | /usr/bin/grep default"

it works. Why does NSTask mess up with it?

----
You clearly have very little idea of what gets interpreted by the shell and what doesn't. Some reading would be in order here. Your code and your shell command are *not* equivalent. Your code is equivalent to this:

    
/bin/bash -c "\"/usr/sbin/netstat -rn | /usr/bin/grep default\""


In other words, the quotes in your string are being passed as part of the argument. There is no shell modifying these arguments, remember!

----
I presume that the previous reply was not intended to be as belittling as it sounds. A more polite answer would have simply been to show the correct code:
    
[proc setLaunchPath:@"/bin/bash"];
[proc setArguments:[NSArray arrayWithObjects: @"-c", @"/usr/sbin/netstat -rn | /usr/bin/grep default", nil]];


----
Alternatively, you can use NSTask with NSPipe to achieve the same result without a shell:
    
NSTask *netstat = [[NSTask alloc] init];
NSTask *grep   =  [[NSTask alloc] init];

[netstat setLaunchPath:@"/usr/sbin/netstat"];
[netstat setArguments:[NSArray arrayWithObject:@"-rn"]];

[grep setLaunchPath:@"/usr/bin/grep"];
[grep setArguments:[NSArray arrayWithObject:@"default"]];

NSPipe *pipe = [[NSPipe alloc] init];

[netstat setStandardError:[NSFileHandle fileHandleWithNullDevice]];
[grep setStandardError:[NSFileHandle fileHandleWithNullDevice]];

[netstat setStandardOutput:pipe];
[grep setStandardInput:pipe];

pipe = [[NSPipe alloc] init];

[grep setStandardOutput:pipe];

[netstat launch];
[grep launch];

NSData *data = [grep standardOutput] fileHandleForReading] readDataToEndOfFile];
[[NSString *string = [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];


----
The reply was intended to sound exactly as it sounded. If you don't like it, then tough cookies. The asker was pretty clearly cargo-culting, and pointing that out is helpful. As for showing the correct code, sure, I could do that, but when the right code is a small modification off the wrong code which has already been shown, I would hope that merely pointing out the error is enough!

----

"cargo-culting".... lol. I had to look that up. There can be a lot of that around here.....
http://en.wikipedia.org/wiki/Cargo_cult_programming

----
Hey, I am the asker. Well, cargo-culting :-), interesting definition. So, everyone who has a programming-problem out ouf missing knowledge/experience and asks for help is doing "cargo-culting" or what? 
Thanks to the author of that nice and polite answer! :-) --Ben

----
When you blindly copy the quotes from the shell command to a completely different situation without first researching the two, yes, you are cargo-culting. Cargo-culting consists of seeing something done, then attempting to copy it without understanding it. This is exactly what I saw being done here with your quotes, pipes, and so forth.

----

(FYI: I'm the guy who posted the link to the cargo-culting wiki page. No offense was intended. I just thought it was funny).
I think this really is a problem that is easy for a beginner to run into. Especially a beginner who isn't particularly UNIX savvy. I don't think Apple's doco makes any mention of this - it's just something your "assumed to know".

----
If you're doing UNIX programming (and Cocoa is always UNIX programming, particularly when you're launching subprocesses), then you really ought to know about     argv/    argc and how arguments are passed and parsed. If you don't know, well, find out! Ignorance is nothing to be ashamed of as long as you're willing to fix it, just don't get upset when somebody points it out.

The docs for     setArguments: state exactly what happens. In particular, they say, "The strings in arguments do not undergo shell expansion", and, "[passed] to the task via argv[]". To someone who has written a program that reads command-line arguments and does something with them, this is fairly clear. To someone who has not done this, now is the time to make one. I guarantee the experience will be educational.

----
Indeed your correct, the doco does mention it. But I'm still not surprised that many novice developers get bitten by this....

----
Nor am I. How something like     grep "foo bar" < baz gets turned into actual program arguments, and which areas the shell and the program each handle, is often not something that is mentioned. The mention of cargo-culting was not meant to be antagonistic, but just pointing out that the approach was not a good one, and that more education was in order. If I screw something up because I have no clue as to what's going on, I would personally be happy if someone told me where my ignorance lay and what I should research.

----
Well, let's end that discussion peaceful. I did try lots of combinations with or without quotes and god knows why I wasn't doing this:

    
[proc setLaunchPath:@"/bin/bash"];
[proc setArguments:[NSArray arrayWithObjects: @"-c", @"/usr/sbin/netstat -rn | /usr/bin/grep default", nil]];


However, I felt pissed-off by the second answer ("You clearly have very little idea ...") - even if you are right. I don't believe that this page if for telling people they should rtfm. But, again, ok now. End of discussion, thanks for the help :-) -- Ben

----
In my opinion, the only place where "rtfm" is not appropriate is when money is changing hands. If you have a lack of knowledge that can be remedied by tfm, you'd better expect to be told to r it!

----
You are welcome, Ben. -- Mike
RTFM: Manners matter.

