---
layout: page
title: GeneratingAndAttachingManyToolTips
---

I have a program which is subdivided into many parts. Each part has its own GUI(Or should I say View?) of several NSViews and NSWindows, and executes its own NSTask. Each part also has its own Controller.

The essential problem is this: each part, with its own NSView and NSWindows, is full of NSTextFields that need tooltips. For the whole program, this probably adds up to 300+ or so tooltips. A lot of these NSTextFields are repeats of each other, and can use the same tooltip strings. What I'm looking for is a good way to set all these tooltips without having to explicitly say, [<NSTextField name> setToolTip:<blah>] a ton of times in each Controller. I already have the IBOutlets to the NSTextFields in NSArrays for every Controller.

My thought was to create a central NSDictionary, with the tooltip strings as the objects and something as the key. The question is, what to use as the key. The best thing would be to use the variable names of the NSTextFields, but I don't know if there's a way to get these into NSString form.

----

It's too late at the moment for me to be able to think of how, but CocoaBindings feels like it could help here...

----

Have you tried using the NSTextField itself as the key? You're *supposed* to be able to use any id as a dictionary key, but I recall this not working in the past. Maybe it's fixed now.

----

I gave that a shot before I posted, yeah. It compiles, but it posts an error while it runs (copyWithZone, as I remember). That was the closest way I could think of to using the variable name as the key. I've been trying to think of a way to convert a variable name to an NSString, but haven't so far.

----

NSDictionary keys need to implement NSCopying, so you can't just use any object at all. That's why that idea failed.

Have you considered using the fields' tags somehow?

The way to get an NSString from a variable called     someVariable is to write     @"someVariable". I'm not sure what you were looking for other than that.

----

I made a simple project in Xcode and added several NSTextFields to the main window. For each NSTextField that I wanted to set a ToolTip on, I set the tag to 1024. I created a Controller class with a single outlet connected to the main window. Then in the Controller awakeFromNib method, I wrote this:

    
- (void)awakeFromNib;
{
    if (window != nil)
    {
        NSView * contentView = [window contentView];
        NSArray * subViews = [contentView subviews];
        NSView * subView = nil;
        NSEnumerator * viewEnumerator = [subViews objectEnumerator];
        
        while (subView = [viewEnumerator nextObject])
        {
            if ([subView isKindOfClass: [NSTextField class]])
            {
                if ([(NSTextField *)subView tag] == 1024)
                {
                    [(NSTextField *)subView setToolTip: @"I am a tool tipped text field"];
                }
                else
                {
                    [(NSTextField *)subView setToolTip: @"I am NOT a tool tipped text field!"];
                }
            }
        }
    }
}


This is a very simple implementation and should be re-written recursively search all views/subViews within the window, but it works for the purposes of demonstration. When the app is run, only the NSTextFields with a tag of 1024 have a tool tip associated with them (**I am a tool tipped text field**) with a default tool tip used for all other NSTextFields with a tag value other than 1024.

So all you'd have to do is establish a scheme by which you assign unique tag values to all of the NSTextFields that you wish to share a given tool tip. Of course, you'll have to write a bit more code and you should probably abstract this whole process to a class of its own. Then you can define all of your tool tips and tag mappings in one place. There are many different ways to approach this kind of problem, but hopefully this example will serve to spawn some ideas of your own.

----
I see what you mean. I thought about using tags too, but I was hoping to find a way to just traverse right through the NSArray of NSTextFields and assign them tooltips without having to set a tag for every NSTextField in the app. I guess there's no other way to do it, so I'll just bite the bullet and assign tags to everything and keep a document somewhere with what tag number is what text field.

----

You could just use #defines to map tags to text fields. Or an enum

----

I'd subclass NSTextField so I could have the delegate handle the tooltips...

    
// NSTextField subclass...
- (void)textDidChange:(NSNotification *)aNotification
{
id delegate = [self delegate];
    if (delegate && [delegate respondsToSelector:@selector(gimmeATooltip:)]) 
        [delegate gimmeATooltip:self];

   [super textDidChange:aNotification];
}


    
// the delegate...
- (void)gimmeATooltip:(NSTextField *)sender
{
    if ([yourTextfieldArray containsObject:sender]) [sender setToolTip:@"Whatever"];
   else [sender setToolTip:@""];
}


YMMV, void where prohibited.
-NRS

----

I think your NSDictionary idea was the right one, but you have to do a little magic because it doesn't support keys that don't support NSCopying (which NSTextField doesn't, because it wouldn't make any sense). Instead, wrap your keys: create a class MyPointerKey that extends NSObject, implements NSCopying, and contains a single id. Provide isEqual: and hash: implementations that simply compare pointers and return the pointer, respectively, and a copyWithZone: implementation that returns self (assuming you keep MyPointerKey immutable).

Now, have your text field say:

    
[self setToolTip: [toolTipDictionary objectForKey:[[[MyPointerKey alloc] initWithID:self] autorelease]]];


To populate the dictionary, you can do:

    
NSMutableDictionary * myDict = [[NSMutableDictionary alloc] init];

[myDict setObject:@"foo" forKey:[[[MyPointerKey alloc] initWithID:textField1] autorelease]];
[myDict setObject:@"bar" forKey:[[[MyPointerKey alloc] initWithID:textField2] autorelease]];


I'm fairly new to Cocoa, but that should do the trick you need, and can be used with any class at all to stick it in a dictionary by identity. Now, how you grab pointers to the text fields in the first place is up to you, but I'm assuming you have some process for that since you suggested the idea above...

-- AndrewGeweke

