---
layout: page
title: MemoryManagementWithDistributedObjects
---

Still no solution! Need help :)

retainCount on server side is 2! but when I do release recieved object, it sigbuses�

----

What happens, when I return object, when called remotely?
It seems, like it is eating my memory!
This is my method, wich is called from other application:
    
-(void)awakeFromNib
{
    NSString *serviceName = [NSString stringWithFormat:@"test1"];
    NSConnection *serverConnection=[NSConnection defaultConnection];
    [serverConnection setRootObject:self];
    [serverConnection registerName:serviceName];
}

- (NSData *) test
{
	NSData *archive = [NSArchiver archivedDataWithRootObject:objects]; // objects is HUGE. say, 100MB.
	[objects release];
	return archive;
}


When I call this method via proxy, every call increases process consumed memory for about 100MB�

----

What and where is the code calling the method 'test'?

----

Simple setup of NSProxy object with NSConnection, and next call of method.

----

Try using the DO modifiers in you method declarations (must be in a formal protocol...) bycopy/byref. Also, how about better engineered solutions... If there's only certain ways that you will query/modify that data, why not ask the app that owns the data to do the modification.  Or if the data isn't really something owned by the process, why query it to get the data using DO rather than, say having the process you want to query/modify the data open the file itself? (it could even use DO to the first app to get the file path that it should use).  Basically think through the roles of the processes like you do objects.  If the "server" process is supposed to be in charge of that data, let it do the modification.  Rather than expose it's internals, let it expose it's behaviour with a richer communication protocol.  Otherwise if the "server" just happens to know where the data is, but doesn't "own" the data per se, maybe it would be better just passing a note back telling the "client" where to find the data by itself.

----

There is no way to do better solution, just because it is cluster, and the data computated on single machine (returned in test) will be inserted on main machine in the big-scary-machine-doing-some-jobs :)

