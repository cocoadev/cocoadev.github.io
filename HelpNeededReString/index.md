---
layout: page
title: HelpNeededReString
---

NSRange  fixRange=[fileName rangeOfString:@"."];
NSString  *fileRealExtension=[fileName substringFromIndex:fixRange.location];

that takes a string from position of first '.' (substringFromIndex:fixRange.location, searching from the left.

I personally want it from the right, as i am using it for taking a file extension.

eg, if a file is called: bob.sam.jpg

then my code would currently return fileRealExtension=.sam.jpg
i want my code to return fileRealExtension=.jpg

can anyone help?
e-mail me at wispsw@hotmail.com
or post here...

i am new to this btw

- jeremy

----

You could use:

    
fileRealExtension=[fileName pathExtension];


or 
    
fileRealExtension=fileName componentsSeparatedByString:@"."] lastObject];


or similar code to what you were using:
    
fixRange=[fileName rangeOfString:@"." options:[[NSBackwardsSearch];


Note that the first two methods will just return @"jpg" without the dot at the start... there are several NSString methods you could use to make a string with the dot in front.

I'd say the first is the best if you want the file name extension, since it's especially for that and it's been done for you already... but the other methods might be useful to you for other things.

-- AngelaBrett

