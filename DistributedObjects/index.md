---
layout: page
title: DistributedObjects
---



DistributedObjects (aka DO) refers to FoundationKit's Object Oriented RemoteProcedureCall (RPC) mechanism. It allows objects in one process on one machine to talk to objects in other processes on other machines. For more information on using this powerful system, see the FoundationKit's NSConnection object.

NSConnection used to use MachMessages to communicate with other machines. Now NSConnection uses TCP/IP to communicate with other machines, but continues to use (by default?) MachMessages when communicating with processes on one machine.

There are some DO examples on the system here:

/Developer/Examples/Foundation

Also the NSConnection documentation outlines several examples of how and when to use DO.

See also: DistributedObjectsSampleCode, DistributedObjectsForInterThreadCommunication

