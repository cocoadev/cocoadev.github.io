---
layout: page
title: GettingWebAddressOfFiles
---

I want to get the URL from a file I have in my Sites folder. I've tried with fileURLWithPath in NSURL, but what I get is file://localhost/Users/username/Sites/filename and I want the adress that could be used from another machine; something like http://www.domainname.com/~username/filename.
How can I get this address?

----

You would have to create it. Get the IP address using     [[NSHost currentHost] address] and then use NSString's     stringByAppendingPathComponent method to append to that the current OS X user name and then the file name. That make sense?

----

I'm not sure what the best way to get the username is, but since the path "~" applies to the /User/<username>/ folder, you could maybe do...     @"~" stringByExpandingTildeInPath] lastPathComponent] (untested!)

----

There's also a [[NSHomeDirectory() function

----

Or if you are just looking for the user's name, NSUserName()

----

Something weird (to me) is happening when I delete the last component of a URL's absoluteString :

    
NSMutableArray *filesArray = [NSMutableArray arrayWithArray:[openPanel URLs]];
NSLog(@"absoluteString : %@", filesArray objectAtIndex:0] absoluteString]);
[[NSLog(@"cuttedString : %@", [[filesArray objectAtIndex:0] absoluteString] stringByDeletingLastPathComponent]);


It returns :

    
2006-03-03 12:55:46.062 SAM[1383] absoluteString : file://localhost/Users/stephane/Desktop/caution.gif
2006-03-03 12:55:46.063 SAM[1383] cuttedString : file:/localhost/Users/stephane/Desktop/


As you can see, there is a "/" missing after "file:" on the second log.
What is happening?

----
These functions are intended to work on a path, not a UR. It's seeing the //, which is redundant in a path, and eliminating the redundancy. You should work only on the path part of your URL if you want to use path utilities to manipulate it.

----
You're right, I used     [[filesArray objectAtIndex:0] path]); instead of     absoluteString, worked on the path, and then built the NSURL object with the     fileURLWithPath: method and it's working now.

