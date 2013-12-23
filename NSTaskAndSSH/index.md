---
layout: page
title: NSTaskAndSSH
---

Any help on this would be appreciated. I am trying to launch an SSH session from a NSTask, but keep receiving the following errors on launch:
    
Pseudo-terminal will not be allocated because stdin is not a terminal.
Permission denied, please try again.
Permission denied, please try again.
Permission denied (gssapi,publickey,password,keyboard-interactive).

Does anybody know of a way to set a NSPipe or NSFileHandle to emulate a terminal?
Currently my relevant code looks as follows:
    
myTask = [[NSTask alloc] init];
inputPipe = [[NSPipe alloc] init]; 
inputHandle = [inputPipe fileHandleForReading];

[myTask setStandardInput:inputHandle];
[myTask setLaunchPath:@"/usr/bin/ssh"];
[myTask setArguments:[NSArray arrayWithObjects:@"-l", [kci UserName], Location, nil]];

[myTask launch];

The basic problem that I am attempting to overcome is the need to pass in to the password to SSH after the task has been launched. If anybody can direct me to a good resource for this I would be grateful. So far Google, Apple Discussions, and macoshints have all turned up dry.

----

An easy solution is to generate a public/private key pair with ssh-keygen, that way, ssh won't request a password.

----

Thanks for the quick responce, but unfortunately due to the setup/restrictions that the program may have to operate under ssh-keygen is not an option.

----

Try this commad format: ssh name:password@domain

----

as logical as that would seem, it doesn't work. ssh still requests a password.
having done some more digging it appears that I need to set two environmental variables. SSH_ASKPASS and DISPLAY. I know that these can be set by calling setEnvironment:... but I have no clue as to what they should be set as.
anybody have an idea?
Thanks

----

I believe DISPLAY is used for to designate an XWindows display for ssh to use during an X terminal session.

----

Take a look at cvsFinder which runs ssh and reads output. 
http://cvs.sourceforge.net/viewcvs.py/cvsfinder/src/cvsCommands/CVS_Module.m?rev=1.3&view=auto
    
			// see CVS_sshGetPassword for more on how SSH_ASKPASS works
			NSString *keyArray[] = {@"CVS_RSH", @"DISPLAY", @"SSH_ASKPASS"};
			NSString *valueArray[] = {@"ssh", @"localhost", NULL};
			NSBundle *bundle = [NSBundle bundleForClass:[self class]];
			valueArray[2] = [bundle pathForResource:File_sshPassTool ofType:@""];
			dict = [NSDictionary dictionaryWithObjects:valueArray
						forKeys:keyArray count:3];
			DEBUGS(NSLog(@"Env is %@", dict));


----

Note:
According to

http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSTask.html#//apple_ref/doc/uid/20000317/BJFDJCCA 

passing the filehandler for a NSPipe is a BAD IDEA. You should instead pass the pipe directly. IE use the following code.
    
myTask = [[NSTask alloc] init];
inputPipe = [[NSPipe alloc] init]; 
inputHandle = [inputPipe fileHandleForReading];

[myTask setStandardInput: inputPipe];
[myTask setLaunchPath:@"/usr/bin/ssh"];
[myTask setArguments:[NSArray arrayWithObjects:@"-l", [kci UserName], Location, nil]];

[myTask launch];


You can use the inputHandle to access the pipes contents with out a problem.

