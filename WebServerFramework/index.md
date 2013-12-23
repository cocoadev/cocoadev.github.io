---
layout: page
title: WebServerFramework
---

----

This is a simple HTTP server framework written in Objective-C which you can 
embed in your Foundation and Cocoa applications. It provides support for 
GET, HEAD, PUT and DELETE requests, although I am not sure POST requests are
currently handled correctly. Used in a Cocoa application, you will need to
embed the framework in your "Frameworks" folder of the application bundle. To
use in a Foundation tool, you will need to create a folder in the parent folder
to the folder which contains the application called "Frameworks".

I am providing this code with a Creative Commons NON-COMMERCIAL license. 
Please read the "LICENSE". I would appreciate your requests for bugfixes
and features so I can improve this framework for everyone, thanks - General/DavidThorpe.

Download it here:

http://mutablelogic.com/cocoa/General/WebServerFramework.zip (52K)


----

STARTING UP THE SERVER

To create a web server in your application. create an instance of General/HTTPServer and
set the delegate:

    
  General/HTTPServer* theServer = General/[[HTTPServer alloc] init];
  Application* theApplication = General/Application alloc] init];

  // set server's delegate to be the application
  [theServer setDelegate:theApplication];


You can set the port number and the name of the server before starting it:

    
  [theServer setName:@"Name for server"];
  [theServer setPort:8080];


If you don't do this, it will choose an available port and use a default name. You
should tell the web server which HTTP methods you'll accept. These are global
options at the moment and there is no concept of <Directory> and <Location>
statements like apache yet:

    
 [theServer setOptions:([[HTTPMethodGET | General/HTTPMethodHEAD | General/HTTPMethodPUT)];


When starting the webserver, an error code may be returned, a success value will
also be returned:

    
  General/NSError* theError = nil;
  [theServer start:&theError];
  if(theError) {
    // handle the error
  }


This will register the server with bonjour as well, so it will automatically be
available to bonjour-enabled applications, like Safari.

The Web Server uses run loops for processing (not threads) so if this is a
Foundation application you will need to use a run loop for processing. Here's
an example way to create a run loop:

    
  // start the run loop
  double resolution = 300.0;
  BOOL isRunning;
  do {
    General/NSDate* theNextDate = General/[NSDate dateWithTimeIntervalSinceNow:resolution]; 
    isRunning = General/[[NSRunLoop currentRunLoop] runMode:General/NSDefaultRunLoopMode beforeDate:theNextDate]; 
    // occasionally re-create the autorelease pool whilst program is running
    [pool release];
    pool = General/[[NSAutoreleasePool alloc] init];            
  } while(isRunning==YES);  


----
STOPPING THE SERVER

You can stop the server using the "stop" method:

    
  [theServer stop];

  
This also returns a boolean success value.

----
HANDLING REQUESTS

You will need to handle requests in your delegate. At the moment, simple
"what is the path for this operation" requests are provided. For each request,
there are several phases.

 
* <code>-(General/NSString* )pathForGetOrHeadRequest:(General/HTTPRequest* )theRequest;</code>
   This method is called for GET and HEAD requests. You need to return the path to
   the resource that is required. If you return nil, a "Not Found" 404 status code
   is returned to the client.


* <code>-(General/NSString* )pathForPostRequest:(General/HTTPRequest* )theRequest;</code>
   I don't think this one works yet. The theory is that you would treat it like a
   GET request.

* <code>-(General/NSString* )pathForPutRequest:(General/HTTPRequest* )theRequest atomically:(BOOL* )atomicWrite;</code>
   With this one, data is sent from client to server, and you will need to return
   the path of a filepath you would wish to write to. If you return nil, then a
   "Forbidden" status code is returned to the client. Additional checks are
   performed on the server-side to ensure the path can be written to.

   If you want to write atomically on the server-side, then a temporary file is
   created first on the same volume as the desintation file and then the
   destination file is created. You will need to set the 'atomicWrite' variable
   to YES to perform an atomic write.

   The server either returns a "Success" or "Created" status code to the client
   depending on whether a new resource was created or an existing one overwritten.

* <code>-(General/NSString* )pathForDeleteRequest:(General/HTTPRequest* )theRequest;</code>
   If you want to delete a file or a whole directory on the server, you can return
   the path to the resource. If you return nil, "Forbidden" status code is 
   returned to the client.

* <code>-(void)data:(General/NSData* )theData forRequest:(General/HTTPRequest* )theRequest;</code>
   Data from the client is passed through a delegate method to allow you to handle
   POST requests, although as I say this may not work correctly yet:

* <code>-(void)finishedRequest:(General/HTTPRequest* )theRequest error:(General/NSError* )theError;</code>
   When a request is finished, this method is called. There may be an optional
   error code. If no error occurrred, then "theError" is nil.



----
LIMITATIONS AND ADDITIONS

There are lots. POST requests don't work, and no attempt is made to parse GET arguments or POST bodies. It isn't possible to return dynamically-generated data to the client yet - only files on the filesystem. No check is made to make sure there is enough free space on the filesystem before PUT requests are honoured. Keep-alives are not implemented, but the server doesn't yet close the connection after the end of a request (I think). If-Modified-Since headers are not honoured, neither are Range: headers. It expects UTF8 character encoding throughout.

In addition, MAKECOL, PROPFIND, COPY and MOVE requests should be implemented later, as should per-directory and location handling. Maybe some sort of authorization and authentication based on IP address or local user authentication. None of these things are currently part of the framework.


----
EXAMPLE CODE

There is a simple example in the project file which is a mini-web-server which 
uses your home directory as the document root. I've included a convenience method in the General/HTTPRequest
method which will resolve to a path, given a server root:

    
General/NSString* thePath = [theRequest pathWithRoot:General/NSHomeDirectory() followSymlinks:YES];  


The "followSymlinks" variable is meant to be like Apache's General/FollowSymLinks option. It just makes sure
that what you're accessing is actually under your root path, if set to NO. Be careful - this whole
HTTP Server thing is a security risk. When you're running your server under your home directory,
anyone could now access files across the network (unless firewalled). Certainly, don't enable the
DELETE method unless you know what's going on!

----
CODE FROM ELSEWHERE

Some code was inspired by Apple's General/HTTPServer, and the files "General/TCPServer.h" and "General/TCPServer.m"
are pretty much cut and paste in some sections. Their example code is here:

http://developer.apple.com/samplecode/General/CocoaHTTPServer/index.html

There's also a tutorial on O'Reilly's "Mac Dev Center" on doing something similar:

http://www.macdevcenter.com/pub/a/mac/2006/11/14/how-to-write-a-cocoa-web-server.html

Neither of these work well for POST and PUT requests.
----

Nice effort! Thanks for sharing your work. -- General/RobRix
