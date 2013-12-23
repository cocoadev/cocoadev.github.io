---
layout: page
title: POSTMethodANDNSURLRequest
---

I'd like to use the HTTP POST method to get my Cocoa app to communicate with my server.  I've already written code using NSURLConnection and NSURLRequest to download a given URL, but now I want to extend this to be able to request a URL that has POST data in the body.  Eventually, I want to be able to send a file from my Cocoa client to my server through POST.

I see that there is a method 
    
- (NSData *)HTTPBody


of the NSURLRequest class...but this just tells you what the body is...it doesn't allow you to change it.  I think what I need to be able to do is set the body to be what I want.

Anyone know about this?  Thanks.

----

You need to create an NSMutableURLRequest and use the -setHTTPMethod: method to set it to post, and then -setHTTPBody: to set the POST command body; OTOH, you could look at CurlHandle (http://curlhandle.sourceforge.net/ ), which supports POST, and has the handy -setPostDictionary: method, which turns a dictionary of name-value pairs into a form-encoded POST body.  -- Bo

----

Thanks for the refs.  I took a quick look at CurlHandle and it doesn't seem to compile under XCode.

----

The distribution should include a pre-compiled framework in the '10.2 Compatible' folder.  Simply include that in your project. -- Bo

----

So yes, there is a pre-compiled version (10.2) in the distro.  And I put that in /Library/Frameworks and I am able to compile the CURLHandle Tester program.  But the reason I wanted to recompile the tester was cause the binary it came with didn't seem to handle SSL.  They give a test SSL URL in the Tester program, and that didn't work.  I also tried a couple other https sites I know of and none of them worked. And, when I recomiled the Tester, SSL still didn't work.

So I tried recompiling the Framework, but that gives an error in 10.3.  So I'm not sure about using this for my project.

I'd like to use the most straightforward and "cocoa accepted" method of communicating with a server over HTTP(S) via GET and POST.  But it seems like there aren't well-trodden paths....which seems unbelieveable.  Maybe the NSMutableURLRequest is the way to go, but I'm having a hard time figuring out how to set up a file upload POST request for the server.

----

Well, I looked at it, and it's doing a bit of funkiness with a shell script  that assumes that libcurl is in /usr/local/lib/ not /usr/lib/.  However, all I needed to do to make it work was copy libcurl.a out of /usr/lib and into the CurlHandle project directory, include that in the CurlHandle project and then remove the Shell Script phase that was giving the error entirely.  -- Bo

----

Thanks a ton Bo, but I'm still stuck.  I deleted the script build phase from the project.  Then I copied the libcurl.a file from /usr/lib to CURLHandleSource.  Then I tried recompiling but it didn't work (what's weird is that I'm not able to view what the error is...it just tells me that there is an error and a few warnings...I can view the warnings though).  Then I tried going to the Project menu -> Add Files, and I added libcurl.h.  Recompiling...no help.  Finally I tried dragging the libcurl.a framework into the Linked Frameworks section.  But again, no help.  I have a feeling that what I'm not doing right is when you tell me to "include that in the CurlHandle project".

----

What is the error message you're getting?  You should be able to pull up the splitter at the bottom of the build window to see what it is.  I got a couple of warnings about not being 10.1 compatible, but, unless you need 10.1 compatibility, I wouldn't worry about that.  -- Bo

----

That's the thing. It doesn't show me the error, only the warning.  I get 3 warnings, all of which seem to just be inserted by the author telling me, like you said, stuff about 10.1, which I definitely do not need.  When I build it says at the top "Build failed: 1 error and 3 warnings" (I could have the exact syntax wrong on that since it's from memory).  But when I do the usual of going to the Errors and Warnings on the left, it only shows warnings.  My instinct is that it's some sort of "pre-compiling" error...like something wrong with the way I have the structure of the project that's making it fail really early on so that it doesn't tell me what's going wrong.

I tried something else though...I copied libcurl.a into /usr/local/lib/ and then grabbed a fresh copy of the source and tried compiling that...and this time it compiled for a longer time and I did get a visible error.  Under Errors and Warnings on the left a "General" item appears.  Clicking on that shows me the error, which begins "Undefined symbols: _Curl_freeaddrinfo _Curl_global_host_cache_dtor ..."  The Location of the error is "CURLHandleSource:0".

----

That's telling you that the linker can't find the libcurl.a library.  You can fix this manually by going to the 'Search Paths' section of the target settings (which you get to by double-clicking in the CurlHandle target in the project browser) and adding a directory where you know a libcurl.a file exists (like /usr/lib or /usr/local/lib for example) under the Libraries section.  If you use Add Files... to add the library to your target, it should do this for you automatically.  If you're still having problems after this, I'll be happy to post both my modified project and the framework I compiled for you (which I tested using their tester and supports SSL).  -- Bo

----

Thanks Bo, got it working.  What I believe I had done wrong was choose "Add Files..." instead of "Add Frameworks..." when adding libcurl.a.  Anyway, I was able to compile the Framework.  Then I move the resulting Framework to /Library/Frameworks. Then I loaded up the Tester source, and chose "Add Frameworks..." and selected my newly compiled Framework.  And it worked!  SSL and all.

----

Congrats.  -- Bo

