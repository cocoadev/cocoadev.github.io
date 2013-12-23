---
layout: page
title: PlistNSMatrix
---

Hi All,
       I've been recently working with Plists and I have a NSMatrix (2 radio buttons) and i was wondering how to set in my Plist which is the selected cell/RadioButton??

----

I'm assuming your plist is a dictionary.
    
// Assume these are initialized elsewhere
NSDictionary *myPlist;
NSMatrix *myMatrix;


If your matrix is horizontal, do this:
    
NSNumber *selectedRow;
selectedRow = [NSNumber numberWithInt:[myMatrix selectedRow]];

[myPlist setObject:selectedRow forKey:@"SelectedRow"];


or if it's vertical:
    
NSNumber * selectedColumn;
selectedColumn = [NSNumber numberWithInt:[myMatrix selectedColumn]];

[myPlist setObject:selectedColumn forKey:@"SelectedColumn"];


-- Ibson

----

My NSMatrix is horizontal and the above code worked. Thanks alot... Now after reading from the plist file how do i assign the value? I ahave tried this but does not work..

    
[myMatrix selectCellWithTag:(int)[prefs objectForKey:@"SelectedRow"]];


--Syphor

