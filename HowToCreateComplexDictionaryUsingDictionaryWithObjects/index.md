---
layout: page
title: HowToCreateComplexDictionaryUsingDictionaryWithObjects
---




In an app I'm developing I want to be able to save color table specification (or color scheme) files into a designated directory, then let the user pick a file to use to create images (Mandelbrot and Julia set fractals)

The description of a color scheme contains integers, floats, RGB color structs, and an NSARRAY of color band objects. 

I want to create a complex dictionary object that includes a couple of NSArrays. One of those arrays, in turn, will contain a dictionary object, that will contain OTHER structures.

I want to use the dictionaryWithObjects call to map my object's data structures into a dictionary. I'm not sure how to specify the key values for the objects, however. Here's a code snippet:

    
		NSDictionary* temp_dictonary  = [NSDictionary 
			dictionaryWithObjects: 
				[NSArray arrayWithObjects: 
						[NSNumber numberWithInt: gradient_type] 
						[NSNumber numberWithFloat: adjustment_value] 
						//more values here
						[NSArray arrayWithObjects: 
							[NSNumber numberWithFloat: start_color.red] 
							[NSNumber numberWithFloat: start_color.green] 
							[NSNumber numberWithFloat: start_color.blue], nil
						]
						[NSArray arrayWithObjects: 						
							[NSNumber numberWithFloat: start_color.red] 
							[NSNumber numberWithFloat: start_color.green] 
							[NSNumber numberWithFloat: start_color.blue], nil
						]
						[NSNumber numberWithnumberWithBool: use_wobble] 
						[NSArray arrayWithObjects: 						
							[NSNumber numberWithFloat: wobble_hue_change] 
							[NSNumber numberWithFloat: wobble_sat_change] 
							[NSNumber numberWithFloat: wobble_brightness_change], nil
						]
						[NSNumber numberWithfloat: wobble_adjustment], nil
				]
		forKeys:	[NSArray arrayWithObjects: 
			@"gradient_type", 
			@"adjustment_value,
			//>>>>How do I specify the keys for the array and it's objects?>>>
			nil]];


How do I create my array of key objects? Do I mimic the structure of the "array of arrays" that I use to specify my values? What's the syntax?

The data structure above is a "color band" object. It is owned by a color scheme object. The color scheme object has a bunch of instance variables, and then an NSArray of color scheme objects. I'm assuming that once I can pack my color band objects into a dictionary, I can THEN create a color scheme dictionary, defined as its own complex array of values, including the NSArray of color band dictionaries.

Any help would be appreciated.


Duncan C

----

Why is this a dictionary? You might want to consider creating some classes instead. :P

----

I am getting read to write some configuration files to a plist (actually XML) file so the user can load and edit color scheme files outside of the program. After creating the dictionary, I write it to disk with NSDictionary writeToFile:atomically:

I figured out my question after much digging and testing. If you write a dictionary that contains an NSArray to a pList, the entries in the array are entered with indexes. In order to get an entry with a key that contains child values that also have keys, you have to create a dictionary inside another dictionary using nested calls to dictionaryWithObjects 
----
Code formatting fixed. See TextFormattingRules.

----
Thanks for the formatting fix. I'm new to the this site, and didn't know how to do this.

