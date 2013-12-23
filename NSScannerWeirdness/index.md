---
layout: page
title: NSScannerWeirdness
---



if I have an

NSString * myString = @"blah : blah"

why does

[[NSScanner scannerWithString:myString] scanUpToString:@":" intoString:&myResult];

not give me "blah" for myResult?

I have scoured documentation for NSScanner and can't get it to recognize symbol characters such as : and ' 
What am I missing?

----

Your code should work. "myResult" is an NSString pointer right?

source:

    

    NSString *myResult = nil;
    NSString *myString = @"blah : blah";
    [[NSScanner scannerWithString:myString] scanUpToString:@":" intoString:&myResult];
    NSLog(@"myResult: <%@> myString: <%@>", myResult, myString);



output:

    

2004-07-25 16:39:59.734 test[2998] myResult: <blah > myString: <blah : blah>


