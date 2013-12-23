---
layout: page
title: ExecCommandsInHostShell
---

Hello,

I'm trying to exec commands in the host shell after established ssh connection. Like this: 


#      
path     = @"ssh.exp"; 
ssh = [[NSTask alloc] init];
[ssh setLaunchPath:@"/usr/bin/expect"];
[ssh setArguments:[NSArray arrayWithObjects:@"-f",path, host,user,password,nil]];
[ssh setStandardOutput:outPipe];
[ssh launch];

ps=[[NSTask alloc] init];
[ps setLaunchPath:@"/bin/ps"];
[ps setArguments:[NSArray arrayWithObjects:@"-x",nil]];
[ps launch];

# 

The code compiled without any errors. But the problem is that the command is not exec on the host i connected to. The command is executed on my own computer.
Can someone explain how i can establish that?

Greets and Thanks

----
This would probably work better in the forums, just so you know.

To answer your question... are you expecting the /bin/ps call to happen on the host you ssh to? If that is what you want to happen, this will not do it. NSTask is not a shell. The second NSTask you execute is *not* affected by the first one. The first one will (assuming your expect script works), ssh to the remote host and then terminate. Then you run /bin/ps locally. If you want to run a command on the remote host, you will have to do something to the first NSTask to make it happen. For example, connecting with ssh to the remote host will most likely give you a shell on that host. You can then talk to that shell using the ssh task's standard input. Writing "/bin/ps\n" to that standard input will execute it. You can also pass arguments to ssh to have it execute those arguments as a command on the remote computer, which would be another way to accomplish this. But don't think of NSTask as being like a Terminal window, it's not.

----
Yes, indeed i can give one command as argument of ssh like ssh user@host "command",
but i need more than one command. Can you please make a example with the standart input because I tried to set standart input to the tash.
Like this:

[ssh setStandardInput: inputPipe];

Greets and Thanks

