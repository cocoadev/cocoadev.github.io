---
layout: page
title: ReadTableDataFromFile
---

What is the best approach to reading ascii data (delimited by space, comma, tab, etc.) from files using Obj-C/Cocoa?

Example file format: (actual files will be much, much larger)

Col1   Col2     Col3    Col4    ...
1.0     32.0     4.5      5.1     ...
2.0     45.0     19.0    12.6    ... 
3.0     54.0     21.2    23.2    ...
...
...
...

Data would have been originally written using FORTRAN formatted.

The goal would be to read the entire data set into a 2D array for later processing. Unfortunantly, the number of columns & rows of data will not be known prior to the read, however a newline character for each row will exist.   

Any help would be greatly appreciated.

Thanks,
...clark

----

Use NSString stringWithContentsOfFile:encoding:error: to read the data into a string then use NSScanner to parse it.

