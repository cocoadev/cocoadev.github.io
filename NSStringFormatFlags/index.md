---
layout: page
title: NSStringFormatFlags
---



Here are some useful tips for creating strings using standard C format rules:

**number format flags**

If you want to print numbers padded with zeros (e.g. 001, 002, 003) all you have to do is use the "0" flag when formating the string. The zero flag does nothing if you dont specify the number of zeros you would like to pad the formatted string with, so if you want to pad the formatted string with zeros up to a width of 3 characters, follow the "0" flag with a three "3".  

    
    id numberFormatString=[NSString stringWithFormat:@"%03i", 1];
    NSLog(@"numberFormatString: <%@>", numberFormatString);


outputs:

    
2003-07-25 16:44:53.094 test[1673] numberFormatString: <001>


I know to an experienced C programmer this is old news, but I've seen so many newbies lately that I thought I would share this, because when I first started programming, this little fact would have been nice to know.

----

See Using Format Strings: http://developer.apple.com/documentation/Cocoa/Conceptual/DataFormatting/Articles/FormatStrings.html#//apple_ref/doc/uid/20000943-SW1

In the Cocoa documentation for a table of Specifiers

Since NSString uses the exact same format specifiers as     printf, with the sole addition of     %@, a more complete listing of the format specifiers and options that are accepted can be had in     printf's man page: http://developer.apple.com/documentation/Darwin/Reference/ManPages/man3/printf.3.html

