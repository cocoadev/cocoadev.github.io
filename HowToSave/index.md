---
layout: page
title: HowToSave
---

A surprising number of things can be done in Cocoa by just using a line or two of code, or sometimes no code at all.  Unfortunately saving is not one of them.  In this article, I will give you the essential methods for encoding, archiving, retrieving, and decoding information stored in a class.

Basically, when you archive a class, you are saving the values of instance variables (not the methods) for that class into a file .  A file that is saved in this way, needs to be opened by an application that has the same class that was used to save it.

The only way I know to do this is with a "Document-Based Cocoa" project.  If you have already created a project that is not document based, I do not know what to tell you.  To enable archiving, you will need to write code in the class you want to save and in the MyDocument class.

**Preparing your class to encode and decode...**

You first need to write the following line in your class header file.  You write this in between the name of the superclass and the first brace.  This is what you write...
    
<NSCoding>


That is the easy part.  Now you need to write the encoding and decoding methods in your class implementation file.  Here is an example of how you could write them.  Notice each line is encoding or decoding an instance variable for your class...
    
//see later in the page for a usage of key-value coding
-(id)initWithCoder:(NSCoder *)coder
{
    if (self = [super init])
    {
        [self setMovieTitle: [coder decodeObject]];
        [coder decodeValueOfObjCType:@encode(double) at:&left];
        [coder decodeValueOfObjCType:@encode(double) at:&right];
        [coder decodeValueOfObjCType:@encode(double) at:&top];
        [coder decodeValueOfObjCType:@encode(double) at:&bottom];
        [coder decodeValueOfObjCType:@encode(float) at:&totalIterations];
    }
    return self;
}
- (void)encodeWithCoder:(NSCoder *)coder
{
    [coder encodeObject: movieTitle];
    [coder encodeValueOfObjCType:@encode(double) at:&left];
    [coder encodeValueOfObjCType:@encode(double) at:&right];
    [coder encodeValueOfObjCType:@encode(double) at:&top];
    [coder encodeValueOfObjCType:@encode(double) at:&bottom];
    [coder encodeValueOfObjCType:@encode(float) at:&totalIterations];
}

Notice also that there is a difference between the syntax of encoding a C variable, such as a float, long, or int, and a Cocoa object such as an NSString.

You do not need to declare these methods in your class' header file.  The "<NSCoding>" took care of that.  In effect, your class is adopting the NSCoding protocol.  Many other Cocoa classes also comply to this protocol.

**Modifying the MyDocument.m file to allow archiving...**

Once you have certified your class to be a good encoder and decoder, you have to let your MyDocument class know how to archive and unarchive the file (saving and opening for the rest of us).

You should already have the methods in place, if you created this project as document-based.  All you have to do is fill them in with a little bit of code.

For the method...
    
-(NSData *)dataRepresentationOfType:(NSString *)aType {
     return nil;
}

Replace "nil" with
    
[NSArchiver archivedDataWithRootObject:myClass];

Where "myClass" is the name of the class you certified above.

Next, modify the method...
    
-(BOOL)loadDataRepresentation:(NSData *)data ofType:(NSString *)aType {
    [myClass release];
    myClass = [[NSUnarchiver unarchiveObjectWithData:data] retain];
    [self myMethodThatUpdatesTheUserInterface];
}

That is how the method should look like when you are done modifying it.  Of course, the "myMethodThatUpdatesTheUserInterface" is pretty self explanitory.  You need this method in your MyDocument class to copy information from the variables stored in your class into the user interface (such as copying the image back to its NSImageView, or a string back to its NSTextField, etc).

The two other methods in MyDocument.m should look like this...
    
-(void)windowControllerDidLoadNib:(NSWindowController *) aController
{
      [super windowControllerDidLoadNib:aController];
      [self myMethodThatUpdatesTheUserInterface];
}

-(NSString *)windowNibName
{
     return @"MyDocument";
}


And that's all there is to that!  Now, fire up your application and try saving and opening.  The thing that got me is that even "Open Recent" worked once I put in the above code.

    
//keyed archiving is a new feature in Mac OS X 10.2.  It allows for more
//robust and easier-to-read NSCoding code since the order of the
//objects in the archive is unimportant.
//this can also aid with backwards compatibility.
//the below example uses some handy methods of NSCoder instead of just
//-[NSCoder encodeValueOfObjCType:at:].
-(id)initWithCoder:(NSCoder *)coder
{
    if (self = [super init])
    {
        [self setMovieTitle:[coder decodeObjectForKey:@"movieTitle"]];
        left = [coder decodeDoubleForKey:@"left"];
        right = [coder decodeDoubleForKey:@"right"];
        top = [coder decodeDoubleForKey:@"top"];
        bottom = [coder decodeDoubleForKey:@"bottom"];
        totalIterations = [coder decodeFloatForKey:@"totalIterations"];
    }
    return self;
}
- (void)encodeWithCoder:(NSCoder *)coder
{
    [coder encodeObject:movieTitle forKey:@"movieTitle"];
    [coder encodeDouble:left forKey:@"left"];
    [coder encodeDouble:right forKey:@"right"];
    [coder encodeDouble:top forKey:@"top"];
    [coder encodeDouble:bottom forKey:@"bottom"];
    [coder encodeFloat:totalIterations forKey:@"totalIterations"];
}
//to use these NSCoding methods, though, be sure to call
//methods of NSKeyedArchiver and NSKeyedUnarchiver
//instead of their unkeyed counterparts.




Back to HowToProgramInOSX

