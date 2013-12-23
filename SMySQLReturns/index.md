---
layout: page
title: SMySQLReturns
---

Hi all,
       I'm building a cocoa program in Objective C and I'm using the SMySQL framework to interact with my mysql server. Though I am having some problems.

This is my database:

Name: Test DB
-------------

Table Name: TestTable
    
+----------+------+------+-----+---------+-------+
| Field    | Type | Null | Key | Default | Extra |
+----------+------+------+-----+---------+-------+
| TestText | text | YES  |     | NULL    |       |
+----------+------+------+-----+---------+-------+

All Values in: TestTable
    
+----------+
| TestText |
+----------+
| Test     |
+----------+

----------------------------

My Cocoa App code for conecting an getting a result:

    
- (void)getResult
{
    /* MySQL Connection */
    SMySQLConnection *msqlConnection = [[SMySQLConnection alloc] initToHost:@"localhost" withLogin:@"syphor" password:@"syphor" usingPort:3306];
    
    NSDictionary *theDict = [[[NSDictionary alloc] init] autorelease];

    SMySQLResult *theResult = [[[SMySQLResult alloc] init] autorelease];

    NSArray *theFields = [[[NSArray alloc] init] autorelease];

    /* Select the db */
    [msqlConnection selectDB:@"Testdb"];

    theResult = [msqlConnection queryString:@"select * from TestTable"];
    theFields = [theResult fetchFieldsName];
    theDict = [theResult fetchRowAsDictionary];
    
    NSLog(@"Result - %@", [theDict objectForKey:[theFields objectAtIndex:0]]);
}


------------

The Result I get is:

    
Result - <54657374 >


Anyone got any ideas how to get the right result??

----

Dear Anonymous,

Skip these steps:

    
    NSDictionary *theDict = [[[NSDictionary alloc] init] autorelease];
    SMySQLResult *theResult = [[[SMySQLResult alloc] init] autorelease];
    NSArray *theFields = [[[NSArray alloc] init] autorelease];


You're just working over the memory system to no constructive effect.

I will continue, though, with the following disclaimer: all of my MySQL interface experience has been through the totally awesome perl API, and I haven't a lick of experience interfacing to an ObjC app. But I'm game if you are :-)

I'm concenred about your use of "Test DB" and "Testdb" as database names. Are these equivalent? Or are you sure they're correct?

I'd suggest the following lines of code, by way of diagnostics:

    
    NSLog(@"theFields = %@", theFields);
    NSLog(@"theDict = %@", theDict);


It may be the case that "theFields" doesn't have any useful values. In which case, maybe your query is wrong and you should check the usual things: is your database running, are you calling the proper methods, are you checking **all** of the return codes. I suspect your problem is somewhere there ... 

-- MikeTrent

----

Yeah that Test DB and Testdb are just typos... anyway I found out what the problem was....

the SMySQL framework can't work with the field type "text" so I had to change it to varchar(255)... though... that restricts me alot to only 255 characters...

Is there any other way???

--Syphor

IIRC, there exists a C-level MySQL API, which I believe SMySQL and my perl wrappers are built on. You could write your own wrapper to this C API (or just call the C functions directly) to get access to TEXT datatypes. My Perl API definitely allows TEXT datatypes, so it should be possible. Without looking at the SMySQL code, I suspect this is just an oversite?

I see you've already found the SMySQL page on SourceForge. I'd suggest taking the issue up with them directly.

-- MikeTrent

Jim Mooney

The problem I think is the return value your function is returning looks like the memory address (pointer to the NSString object)of the string not the string itself.  How about adding
    <br>NSLog(@"Result - %@", (NSString*)[theDict objectForKey:[theFields objectAtIndex:0] stringValue]);<br>
or 
NSLog(@"Result - %@", (NSString*)[theDict objectForKey:[theFields objectAtIndex:0] description]);

