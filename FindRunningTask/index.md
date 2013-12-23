---
layout: page
title: FindRunningTask
---



I've made a command line tool, that i'll launch with NSTask like this:

    

NSTask * server = [[NSTask alloc] init];
NSMutableArray *args = [NSMutableArray array];
[args addObject:@"Server"];
[args addObject:path];
[server setCurrentDirectoryPath:path];
[server setLaunchPath:[path stringByAppendingPathComponent:@"Server"]];
[server setArguments:args];
[server launch];



so far so good.when the user quit the application, i do not send [server terminate] because it's suppose run in background. My problem is when the user open the application (not the server), and want to terminate the server, how do i do that. My guess is i have to find the process and put it into a NSTask and send [task terminate]. But i dont know how to do that.Tnx

----

If you name the server's executable something unique you can launch a task to kill all tasks running under that unique name. 

    

- (IBAction)killServer:(id)sender {
     [NSTask launchedTaskWithLaunchPath:@"/usr/bin/killall" arguments:[NSArray arrayWithObjects:@"AUniqueServerName", nil]];
}

*

note: killall will only kill tasks that are owned by the user*!

----

Killing all tasks with the same name seems overly crude. I suggest that you use AGProcess to find the task. Since it lets you read the task's environment variables, you can tag it at launch and then be sure that you've found your process by looking for a certain environment variable like this:

    

NSString * const ServerBinaryName = @"Server";
NSString * const ServerIdentifierTagEnvironmentKey = @"2334rqfa";

- (void)launchServer {
    NSTask *server = [[NSTask alloc] init];

    NSArray *arguments = [NSArray arrayWithObjects:ServerBinaryName, path, nil];
    [server setArguments:args];

    [server setCurrentDirectoryPath:path];
    [server setLaunchPath:[path stringByAppendingPathComponent:ServerBinaryName]];

    NSMutableDictionary *environment = [NSMutableDictionary dictionaryWithDictionary:[server environment]];
    [environment setObject:@"" forKey:ServerIdentifierTagEnvironmentKey];
    [server setEnvironment:environment];
    
    [server launch];
}

- (void)killServer {
    NSEnumerator *processEnumerator = [[AGProcess userProcesses] objectEnumerator];
    AGProcess *process = nil;
    
    while (process = [processEnumerator nextObject]) {
        if ([ServerBinaryName isEqualToString:[process command]]) {
            NSDictionary *environment = [process environment];
            
            if ([environment valueForKey:ServerIdentifierTagEnvironmentKey]) {
                NSLog(@"Killing server with pid %i.", [process processIdentifier]);

                if ([process terminate]) {
                    NSLog(@"Killed server.");
                }
                else {
                     NSLog(@"Failed to kill server.");
                }
            }
        }
    }
    
    NSLog(@"Did not find a running server.");
}

*

/Mr. Fisk

See QuitApplicationUsingAppleEvent for faster quit code.

