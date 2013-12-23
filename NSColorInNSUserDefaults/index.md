---
layout: page
title: NSColorInNSUserDefaults
---



I have a lingering question about storing NSColor data in the user defaults dictionary. I've read what there is about having
to transform the color to an NSData object before associating it with a key, but I discovered that plain old

     [ defaultsDict setObject: [ NSColor blackColor ] forKey: @"Text View Background Color" ] 

actually worked, and could be restored from the defaults file. The color returned from a color well
could not be digested by defaults in the same way.

signed,

Curious Cocoa Coder

----

You should use the NSArchiver and NSUnarchiver classes for that. You could also store the color in a short, human readable form(e.g. #000000 = {0,0,0,1} = blackColor):

    

@interface NSColor(Extras)

+ (NSColor *)colorWithString:(NSString *)string;
- (NSString *)string;

@end

@implementation NSColor(Extras)

+ (NSColor *)colorWithString:(NSString *)string
{
    NSScanner *scanner = [NSScanner scannerWithString:string];
    float red = 0.0;
    float green = 0.0;
    float blue = 0.0;
    float alpha = 1.0;
    
    [scanner setCharactersToBeSkipped:[NSCharacterSet characterSetWithCharactersInString:@"{},\n\r\t "]];
    [scanner scanFloat:&red];
    [scanner scanFloat:&green];
    [scanner scanFloat:&blue];
    [scanner scanFloat:&alpha];
    
    return self class] colorWithDeviceRed:red green:green blue:blue alpha:alpha];
}

- ([[NSString *)string
{
    NSColor *rgbColor = [self colorUsingColorSpaceName:NSCalibratedRGBColorSpace];
    float red = 0.0;
    float green = 0.0;
    float blue = 0.0;
    float alpha = 1.0;
    
    [rgbColor getRed:&red green:&green blue:&blue alpha:&alpha];
    
    return [NSString stringWithFormat:@"{ %g,%g,%g,%g }", red, green, blue, alpha];
}

@end


-- JP

----

     [ defaultsDict setObject: [ NSColor blackColor ] forKey: @"Text View Background Color" ]  doesn't work for me I get the following error:

     *** -[NSUserDefaults setObject:forKey:]: Attempt to insert non-property value 'NSCalibratedWhiteColorSpace 0 1' of class 'NSCachedWhiteColor'. 

----

Why create a -string method when -description works just fine.  For example:

    
NSLog(@"%@", [[NSColor blackColor] description]); 
NSLog(@"%@", [[NSColor blueColor] description] );
NSLog(@"%@", [[NSColor colorWithCalibratedRed:.5 green:.5 blue:.5 alpha:1] description]);


will print out:

    2004-08-01 09:07:16.644 MyApp[8352] NSCalibratedWhiteColorSpace 0 1
2004-08-01 09:07:16.658 MyApp[8352] NSCalibratedRGBColorSpace 0 0 1 1
2004-08-01 09:07:16.666 MyApp[8352] NSCalibratedRGBColorSpace 0.5 0.5 0.5 1


So, to save a color just use: 

    [[NSUserDefaults standardUserDefaults] setObject:[[NSColor blackColor] description] forKey:@"A Color"];

You can then create a category method similar to the above +colorWithString except now thanks to -description it will tell you which factory method to use to create your NSColor object.

----
[OP speaking] Yes. Well, I think that is just what the default system complains about when you try to send an object returned from an NSColorWell directly into the defaults dicitonary. That is, NSCalibratedRGBColorSpace is not acceptable.

What are the properties of the object returned from [ NSColor blackColor ] that allow it to go into the database?

----

When you open your applications preference file in Property List Editor, what is the class and what is the value for the key @"Text View Background Color"?

I would check it out for myself, but like I said I get an error.

----

[ OP again, red-faced ] I am somewhat abashed at having dragged this out so long. In fact, I *cannot*  put an NSColor
object directly into the defaults database by using the setObject: forKey: method. I get the same error you do when I try.

What I DID do was add such a key-value pair to an NSDictionary called defaultsDict followed by the
message  registerDefaults (with defaultsDict as its argument ) sent to the shared defaults object.
Because I got away with that, I thought I had actually succeeded in placing the NSColor into the standardUserDefaults.

But even though it is added to the dictionary, the defaults system cannot and does not write that object to the database.
It just doesn't complain about it under those circumstances.

----
Apple actually recommends archiving the NSColor if you need to store it in user defaults.

http://developer.apple.com/documentation/Cocoa/Conceptual/DrawColor/Tasks/StoringNSColorInDefaults.html

Of course, this is an older example, and you should be using an NSKeyedArchiver rather than the deprecated NSArchiver.

*Also see NSUserDefaultsCategory.*

----

*Why create a -string method when -description works just fine.*

Because the output of the description method is not in any way guaranteed - this could easily break in the future!  The description method should *only* be used for debugging.

