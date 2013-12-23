---
layout: page
title: HttpPost
---

 
How to do an http post command within cocoa:

    
 NSString* content = [@"item=" stringByAppendingString:@"Something to Post"];

  NSURL* url = [NSURL URLWithString:@"http://www.url.com/pagetopost"];
  NSMutableURLRequest* urlRequest = [[NSMutableURLRequest alloc] initWithURL:url];
  [urlRequest setHTTPMethod:@"POST"];
  [urlRequest setHTTPBody:[content dataUsingEncoding:NSASCIIStringEncoding]];


After this point you can do several things, such as perform the post in a
WebKit window, or read this for managing the response:

http://www.awprofessional.com/articles/article.asp?p=468381&seqNum=2&rl=1

- Kreeger

