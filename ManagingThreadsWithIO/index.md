---
layout: page
title: ManagingThreadsWithIO
---

	


I have a ThreadWorker thread that is finding my WAN IP by querying a website.  The problem is that the thread wants to return before the query is finished and BOOM.  I have tried putting 	[NSThread sleepUntilDate:[NSDate dateWithTimeIntervalSinceNow:10]]; in there to slow things down but no luck.  How would one manage this sort of situation?

This offending code is in the thread's run loop:

    
 getMyWANIp = @"set theResult to do shell script (\" /usr/bin/curl --connect-timeout 5 -s http://www.whatismyip.com | grep 'Your ip is' | awk '{print $4}'\") as string";
 NSAppleScript * myScript = NSAppleScript alloc] initWithSource:getMyWANIp];
 NSString * wanFromApplescript = [[myScript executeAndReturnError:nil] stringValue];
 MyWANIP = [NSString stringWithFormat:@"WAN: %@", wanFromApplescript];

	
I put it in a thread because it was blocking the UI whenever it ran but I am having no luck with threadworker...

[[EcumeDesJours

----

So, let me get this straight, you're spawning a thread to execute an AppleScript to run a shell script.  My advice is to forget the threading and the AppleScript and just use NSTask to run the shell script.  -- Bo

----

Bo - the reason I used this method is that I am piping the output a few times and it seemed easier to do a one-off shell command.  In retrospect perhaps a bad idea since I have unleashed some demons, t'would seem...
EcumeDesJours

----

The only reason why the ThreadWorker callback would come in before the resource was available is because of some other bug in the code where the returned value isn't properly being saved or sent back through the callback.  The other reason, of course, is that this call is non-blocking in which case the thread would be redundant (but that doesn't sound like the case, given that you said it blocks the UI, otherwise).

My suggestion would be to ditch all of these problems and use a non-blocking NSURL call.

--JeffDisher
----

I'll give it a shot!

EcumeDesJours

----

I'll second what Jeff said, though string operation like regexes are a bit tougher to do in Obj-C than on the command line.  To do a shell command as a string, just use the shell as the launched program and add     -c as an argument to get it to execute from the command line, i.e.     tcsh -c "curl -s blah | blah | blah" (or the shell of your choice).  -- Bo

----

I guess I should probably try it both ways to learn :-)  I just looked at NSURL and got the old routine working with the following code:

    
 int eachLine;
 
 NSURL *url = [NSURL URLWithString:@"http://www.whatismyip.com"];	
 NSString *response = [NSString stringWithContentsOfURL:url];
 	
 if(![response isEqualToString:@""]){
 NSArray * urlElements = [response componentsSeparatedByString:@"\r"];
 NSLog(@"%i",[urlElements count]);
 
 for(eachLine = 1; eachLine <= [urlElements count]; eachLine++) {
        if (urlElements objectAtIndex:eachLine] containsString:@"Your ip is"]){
 		MyWANIP = [[[urlElements objectAtIndex:eachLine] componentsSeparatedByString:@" "] objectAtIndex:3];
 		break;}
 	       }
 	}
 
 else MyWANIP = @"offline";
 


My only concern is what happens if there is no quick reponse from [[NSURL?  how long does stringWithContentsOfURL wait before timing out and return nil?

EcumeDesJours

----

    +stringWithContentsOfURL: will block the thread it's in too.  Use an NSURLConnection (or CURLHandle or a separate thread or a separate process) to download asynchronously.  --BO

----

This is what your looking for NSURLConnection.  You can set up more of the URL download options such as cache policy as well as tell it how long to wait before timing out. It can also work synchronously but then you have to look at setting the delegate functions for handling the returned data, Apple has good example code on this.  This example code here blocks.  --FA

    
 //set up request
 NSURLRequest *theRequest=[NSURLRequest requestWithURL:[NSURL URLWithString:@"http://www.whatismyip.com"]
                       cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:120.0];
 	
  // create the connection with the request
 NSString *results = [[[NSString alloc] initWithData:
                      [NSURLConnection sendSynchronousRequest:theRequest returningResponse:nil error:nil] 
                       encoding:NSUTF8StringEncoding] autorelease];

