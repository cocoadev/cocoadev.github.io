---
layout: page
title: ReplaceOccurrencesOfString
---

I'm a new cocoa programmer and am creating a WebView to handle some of my interface elements. I dug around in some bundles and have noticed that people are loading predefined chunks of html saved in files in the application bundle. These files take the form of standard html with tokens such as @pageTitle defined within them. At runtime these tokens have been replaced with values from other parts of the program.

What's the best way to do this replacement?.

----

Try NSMutableString's replaceOccurrencesOfString:withString:options:range: method.

    
NSString * foo = @"foo";
NSString * formattedFoo = @"<a href=\"http://www.foo.com\">Foo!</a>";
NSMutableString * templateHTML = [[NSMutableString alloc] initWithContentsOfFile:@"template.html"];

[templateHTML replaceOccurrencesOfString:foo withString:formattedFoo 
                            options:NSCaseInsensitiveSearch 
                            range:NSMakeRange(0, [templateHTML length])];
[templateHTML writeToFile:@"finished.html"];
[templateHTML release];


----

I'm reading a text file into an NSString. To replace all of the newlines in the resulting string with spaces
(assuming the original instance     string is already autoreleased):

    
NSMutableString *mString = [string mutableCopy];
[mString replaceOccurrencesOfString:@"\n" withString:@" " options:NSCaseInsensitiveSearch range:(NSRange){0,[mString length]}];
string = [ NSString stringWithString: [ mstring autorelease ]];


----

On a pre-10.2 system when using 

replaceOccurrencesOfString:withString:options:range:

- (unsigned int)replaceOccurrencesOfString:(NSString *)target withString:(NSString *)replacement options:(unsigned)opts range:(NSRange)searchRange

with a NSMutableString. Error message when compiling:

"warning: NSMutableString does not respond to replaceOccurrencesOfString:withString:options:range:"

Code is:

    
-(NSMutableString*)getPattern
{

    NSRange rangeH;

    NSMutableString * patternString;
    
    patternString=[NSMutableString    stringWithFormat: @"%@%@%@ %@%@%@ %@%@%@ %@%@%@ %@%@%@", 
   
    [t11 stringValue],[t21 stringValue],[t31 stringValue],
    [t12 stringValue],[t22 stringValue],[t32 stringValue],
    [t13 stringValue],[t23 stringValue],[t33 stringValue],  
    [t14 stringValue],[t24 stringValue],[t34 stringValue],  
    [t15 stringValue],[t25 stringValue],[t35 stringValue]
    ];
   
    printf("%s\n", [patternString UTF8String]);
    rangeH = NSMakeRange(0, [patternString length]);

    [patternString replaceOccurrencesOfString:@"h" withString:@"G" options:NSLiteralSearch range:rangeH];
    
     return patternString;
}



----

I have compiled similar code using 10.2 (works fine as expected) but when the compiled application is launched on a 10.1 system it does not work.  I thought that the runtime environment was supposed to recognise that the framework of the compiled code is not compatible ("major framework vs. minor framework changes etc") and that the incompatible framework is then used instead of the outdated framework (in this case the framework is the cocoa framework).

PCF

----
I am using 10.4.9 at the moment, and "replaceOccurrencesOfString" also doesn't work, giving a compile error and crashing the programme.

----

PCF: -replaceOccurrencesOfString:withString:options:range: is not available on Mac OS X 10.1 or earlier; it was added in 10.2. If you launch on 10.1, it has no idea what this method is, and so it dies.

As for 10.4.9, if you're getting a compile *error* with an Objective-C message dispatch, your syntax is wrong. If it were just an unrecognized selector, you'd get a warning and compilation would continue.

