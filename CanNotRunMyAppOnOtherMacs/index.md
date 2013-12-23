---
layout: page
title: CanNotRunMyAppOnOtherMacs
---

I can not distribute my app either as an image then an Archive. On my G5, where I developed my app, I dont have problems. It's not a big application, so it should run also on a G3 or G4.
I cleaned my target before I built it in release mode, like always. On my G4 the app crashes always at the same time: When I start it from Desktop then it crashes at a

    
if([index intValue]==26 && [checkBox state]==1)


And when I start the app in the image, it crashes when I uncheck the SwitchButton(checkBox). But not at the if(). I dont think that something in my code causes the crash, because my app runs beautifully on my G5!

I cant help myself! Do you know, why does this happen?

Ludwig

----

Don't think about GX compatibility, think along the lines of Mac OS 10.X compatibility. What OS versions are you running your app on? Also, please PostYourCode - we need a lot more details.

----

Also MAKE SURE you have made a build with the Release BuildStyle (Deployment BuildConfiguration for XcodeTwoDotOne). If you have ZeroLink enabled, the app resulting from the build will only run on the Mac you created it in (unless you also copy the object files and whatever else ZeroLink uses along).

*Don't worry about processors for compatibility most of the time, unless it's a game or something else that's heavy. ZeroLink is to blame for most of these types of problems... or the configuration settings. Remember, you have to change it for the project AND the target panels. What a pain, huh?*

----
It's not ZeroLink! In the Debug Style I have a ZeroLink, but not in Release Style, which is quiet logically! The only difference between my Mac's, is, that I have installed the developer Tools only on my G5. The newest version of Tiger is running on all my Mac's. I must admit, that I am not sure about my code: For example when you look at the pointer "index" it is always treated as an Integer Number, but it is defined in many methods. Here is my code:

    #import "ImageKontroller.h"

@implementation ImageKontroller

- (id)init {
	if (self = [super init]) {
		srandomdev();
		index=0;
		index = [NSNumber numberWithInt:[index intValue]-1];
		NSSet *alkylhalo = [NSSet setWithObjects:@"alkylhalogenid", @"alkylhalogenide", nil];
		NSSet *ether = [NSSet setWithObjects:@"ether", nil];
		NSSet *amin = [NSSet setWithObjects:@"amin", @"amine", nil];
		NSSet *aldehyd = [NSSet setWithObjects:@"aldehyd", @"aldehyde", nil];
		NSSet *halbacetale = [NSSet setWithObjects:@"halbacetal", @"halbacetale", nil];
		NSSet *acetale = [NSSet setWithObjects:@"acetal", @"acetale", @"carbons�uren", nil];
		NSSet *carbonsaure = [NSSet setWithObjects:@"carbons�ure", @"carbons�uren", nil];
		NSSet *ester = [NSSet setWithObjects:@"ester", @"carbons�ureester", nil];
		NSSet *carbonamid = [NSSet setWithObjects:@"carbons�ureamid",@"carbons�ureamide", @"amide", @"amid", nil];
		NSSet *carbonamisek = [NSSet setWithObjects:@"carbons�ureamid", @"carbons�ureamide", @"amide", @"amid", nil];
		NSSet *carbonanhy = [NSSet setWithObjects:@"carbons�ureanhydride", @"carbons�ureanhydrid", @"anhydride", @"anhydrid", nil];
		NSSet *keton = [NSSet setWithObjects:@"keton", @"ketone", nil];
		NSSet *lacton = [NSSet setWithObjects:@"lactone", @"lacton", @"laktone", @"lakton", nil];
		NSSet *chloride = [NSSet setWithObjects:@"carbons�urehalogenide", @"carbons�urehalogenid", @"s�urechloride", @"s�urechlorid", nil];
		NSSet *lactam = [NSSet setWithObjects:@"lactame", @"lactam", @"laktame", @"laktam", nil];
		NSSet *nitril = [NSSet setWithObjects:@"nitrile", @"nitril", nil];
		NSSet *sulfid = [NSSet setWithObjects:@"sulfide", @"sulfid", @"thioether", nil];
		NSSet *enamine = [NSSet setWithObjects:@"enamine", @"enamin", nil];
		NSSet *thiole = [NSSet setWithObjects:@"thiole", @"thiol", @"alkanthiole", @"alkanthiol", nil];
		NSSet *alkine = [NSSet setWithObjects:@"alkine", @"alkin", nil];
		NSSet *enole = [NSSet setWithObjects:@"enole", @"enol", nil];
		NSSet *alkohol = [NSSet setWithObjects:@"alkohole", @"alkohol", nil];
		NSSet *hydrate = [NSSet setWithObjects:@"hydrate", @"hydrat", nil];
		NSSet *imine = [NSSet setWithObjects:@"imine", @"imin", nil];
		NSSet *alkene = [NSSet setWithObjects:@"alkene", @"alken", nil];
		NSSet *alkane = [NSSet setWithObjects:@"alkane", @"alkan", nil];
		NSSet *phenole = [NSSet setWithObjects:@"phenole", @"phenol", nil];
	bezeichnungen = [[NSMutableDictionary alloc] init];
	[bezeichnungen setObject:alkylhalo forKey:@"Bild0"];
	[bezeichnungen setObject:ether forKey:@"Bild1"];
	[bezeichnungen setObject:amin forKey:@"Bild2"];
	[bezeichnungen setObject:aldehyd forKey:@"Bild3"];
	[bezeichnungen setObject:halbacetale forKey:@"Bild4"];
	[bezeichnungen setObject:acetale forKey:@"Bild5"];
	[bezeichnungen setObject:carbonsaure forKey:@"Bild6"];
	[bezeichnungen setObject:ester forKey:@"Bild7"];
	[bezeichnungen setObject:carbonamid forKey:@"Bild8"];
	[bezeichnungen setObject:carbonamisek forKey:@"Bild9"];
	[bezeichnungen setObject:carbonanhy forKey:@"Bild10"];
	[bezeichnungen setObject:keton forKey:@"Bild11"];
	[bezeichnungen setObject:lacton forKey:@"Bild12"];
	[bezeichnungen setObject:chloride forKey:@"Bild13"];
	[bezeichnungen setObject:lactam forKey:@"Bild14"];
	[bezeichnungen setObject:nitril forKey:@"Bild15"];
	[bezeichnungen setObject:sulfid forKey:@"Bild16"];
	[bezeichnungen setObject:enamine forKey:@"Bild17"];
	[bezeichnungen setObject:thiole forKey:@"Bild18"];
	[bezeichnungen setObject:alkine forKey:@"Bild19"];
	[bezeichnungen setObject:enole forKey:@"Bild20"];
	[bezeichnungen setObject:alkohol forKey:@"Bild21"];
	[bezeichnungen setObject:hydrate forKey:@"Bild22"];
	[bezeichnungen setObject:imine forKey:@"Bild23"];
	[bezeichnungen setObject:alkene forKey:@"Bild24"];
	[bezeichnungen setObject:alkane forKey:@"Bild25"];
	[bezeichnungen setObject:phenole forKey:@"Bild26"];
	}
	return self;
}

- (IBAction)NeuesBild:(id)sender
{ 
if([index intValue]==26 && [checkBox state]==1)
	{
		index=0;
		index = [NSNumber numberWithInt:[index intValue]-1];
		[BildFenster setImage:[NSImage imageNamed:@"ende"]];
		
	}
	  
else if([checkBox state]==1)
	{
	[index autorelease];
	index = [[NSNumber numberWithInt:[index intValue]+1] retain];
	NSImage *boud = [NSImage imageNamed:[NSString stringWithFormat:@"Bild%@", index]];
	[BildFenster setImage:boud];
	zahler=0;
	}



else if([checkBox state]==0)
{
	long randomNum = random();
	index = [[NSNumber alloc] initWithInt:randomNum % 27];
	NSImage *bild = [NSImage imageNamed:[NSString stringWithFormat:@"Bild%@", index]];
	[BildFenster setImage:bild];
	zahler=0;
	}
}

- (IBAction)setZero:(id)sender
{
	index=0;
	index = [NSNumber numberWithInt:[index intValue]-1];
}

- (IBAction)BildAnzeigen:(id)sender
{	

	NSString *eingabe = [[[TextFeld stringValue] lowercaseString] stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceCharacterSet]];
	NSString *antwort;
	NSString *myString = [TextFeld stringValue];
	NSString *meinString;
	
	antwort = [NSString stringWithFormat:@"Bild%@", index];
	if(bezeichnungen objectForKey:antwort] containsObject:eingabe])
	{
		meinString = [myString stringByAppendingString:@" ?"];
		[[[TextFeld setStringValue:meinString];
		[TextFeld selectText:self];
		}
	else if([zahler intValue] == 5)
		
		[TextFeld setStringValue:NSLocalizedStringFromTable( antwort, @"Bezeichnungen", @"Ein Kommentar")];
	
	else
		{
		[TextFeld setStringValue:@"Falsch"];
		[TextFeld selectText:self];
		[zahler autorelease];
		zahler = [[NSNumber numberWithInt:[zahler intValue]+1] retain];
		}
}

- (IBAction)loadNib:(id)sender
{
	[[NSNib alloc] initWithNibNamed:@"MainMenu.nib" bundle:nil];
}	
- (void)dealloc
{
	[bezeichnungen release];
	[index release];
	[super dealloc];
}
@end


And for better understanding, here my header file:
    
/* ImageKontroller */

#import <Cocoa/Cocoa.h>

@interface ImageKontroller : NSObject
{	
	NSNumber *index;
	NSNumber *zahler;
	NSMutableDictionary *bezeichnungen;
    IBOutlet NSImageView *BildFenster;
    IBOutlet NSButton *checkBox;
    IBOutlet NSTextField *TextFeld;
}

- (IBAction)loadNib:(id)sender;
- (IBAction)setZero:(id)sender;
- (IBAction)BildAnzeigen:(id)sender;
- (IBAction)NeuesBild:(id)sender;
- (void)dealloc;
@end


I hope you can follow my code. Perhaps I describe my app shortly. My app loads a picture in"BildFenster", when you click on the Button "NeuesBild". Now you can type in the "TextFeld" the name of this chemical structure and press return. If you do that the action "BildAnzeigen" is called and checks if it is the right answer in the Dictionary bezeichnungen. The pictures are showed in random order, unless you check the SwitchButton "checkBox". Then it goes in order trough the pictures to make sure, that you have learned all the chemical structures. The action loadNib is not complete. There I would implement a method to reopen the window when it is closed. But dont look at that, first look at the if's and index;-) I think there might be some problems! The settings are untouched, fortunately!
Thanks in advance!
Ludwig

*I only had time to glance over your code, but the thing that jumps out at me is that you seem to be treating index as a primitive integer value, when it's actually a pointer to an object variable. Replace all your "index = 0;" calls with index = nil, although nil and zero are actually the same, they should not be treated the same way conceptually. Remember that any call to nil will return nil, except in cases where the return value is a primitive type such as float (int may return nil, although I'm not 100% sure off the top of my head, so be careful). Finally, remember that it's an object, and make sure you're releasing and retaining when you should be. Depending on the case, you may be better off defining index as a primitive int, instead of an NSNumber.*


----

    
index=0;
index = [NSNumber numberWithInt:[index intValue]-1];


I see at least two errors here. 

First, you are confusing NSNumber and     int; they are two completely different things.     index is a *pointer* to a NSNumber object, not a value in and of itself. Setting index = 0 does *not* create a NSNumber object of value 0, but instead makes index point at location 0 in memory. 
So, what happens in the second line when you send (argh!) a message to index? the Objective-C runtime sees that index equals zero and, for a miracolous coincidence, a pointer to zero is the same as     nil (that is, a flag value that states that index points to no object). So, the Objective-C runtime returns     nil because it's set to ignore messages sent to     nil by default. (This can be overridden). Miracolously, again, nil == 0, so you are actually sending [NSNumber numberWithInt: 0 - 1] rather than what you are thinking. See MessagingNil. (between us, gcc should have warned you about 'making a pointer out of an integer without a cast', because it's what you're doing -- you're making an integer, 0, a pointer to a NSNumber object by mistake.)

*If you had used a value different than 0 in the first line, like index = 15, the     intValue message would have been sent to the object **at memory location 15**!* Since it's highly unlikely that a NSNumber object is located at decimal position 15, your application would have found itself in nondeterministic waters and would have likely crashed soon afterwards.

Likely, you come from a C# or Java 1.5 background where the wonders of autoboxing would have seen the NSNumber* and turned the 0 into the C# or Java equivalent of a     [[NSNumber alloc] initWithInt:0].

Second, +numberWithInt: returns an *autoreleased* index object. Autoreleased objects are objects that the application thinks are "uninteresting"; they are recycled (removed from memory) at regular intervals (at every spin of the NSRunLoop, to be precise, unless you manage the NSAutoreleasePool yourself.
This means that *the index object you constructed is going to disappear very very soon!*
All methods in Cocoa return autoreleased objects (or objects retained elsewhere) except those that begin with alloc, init or copy. This DOES NOT apply to the sets because NSMutableDictionary retains the object you add to it via -    setObject:forKey:, so they are safe from automatic garbage collection.
Rather than +numberWithInt, you should use [[NSNumber alloc] initWithInt:...].

It's likely that other problems lurk elsewhere in the code, but I don't have time to look at it. Fellow CocoaDev writers, what do you see above?

----

How about *actually posting* the error you get? Saying something is "not working" is a waste of a post. Copy and paste any error messages from your console, etc. that indicates it is 'not working'. I'm 99% sure it *is* in fact ZeroLink and that you're missing something about the way the build styles system works in XCode. Even if I'm wrong, we can't tell you what your problem is because our crystal ball (the one that foretells disaster and lets us read the console logs of random computers across the world) is in the shop.

----

On my G5, where I built my app, I get no error. But here is the crash report from my G4 PB:

    
Host Name:      PowerBook-G4
Date/Time:      2005-09-05 17:28:55.003 +0200
OS Version:     10.4.2 (Build 8C46)
Report Version: 3

Command: ImageView
Path:    /Users/ludwigvilliger/Desktop/ImageView.app/Contents/MacOS/ImageView
Parent:  WindowServer [69]

Version: ??? (2.0)

PID:    391
Thread: 0

Exception:  EXC_BAD_ACCESS (0x0001)
Codes:      KERN_INVALID_ADDRESS (0x0001) at 0x6d656e95

Thread 0 Crashed:
0   <<00000000>> 	0xfffeff18 objc_msgSend_rtp + 24
1   com.apple.CoreFoundation     	0x90742618 CFNumberCreate + 444
2   com.apple.Foundation         	0x92874b4c -[NSPlaceholderNumber initWithInt:] + 200
3   com.apple.Foundation         	0x92874a68 +[NSNumber numberWithInt:] + 56
4   com.apple.myCocoaApplication 	0x00003310 -[ImageKontroller setZero:] + 76 (crt.c:355)
5   com.apple.AppKit             	0x936a4d2c -[NSApplication sendAction:to:from:] + 108
6   com.apple.AppKit             	0x936a4c60 -[NSControl sendAction:to:] + 96
7   com.apple.AppKit             	0x936a4b40 -[NSCell _sendActionFrom:] + 156
8   com.apple.AppKit             	0x936beb58 -[NSCell trackMouse:inRect:ofView:untilMouseUp:] + 1020
9   com.apple.AppKit             	0x936be740 -[NSButtonCell trackMouse:inRect:ofView:untilMouseUp:] + 564
10  com.apple.AppKit             	0x936be164 -[NSControl mouseDown:] + 536
11  com.apple.AppKit             	0x9365f9c8 -[NSWindow sendEvent:] + 4616
12  com.apple.AppKit             	0x93608bfc -[NSApplication sendEvent:] + 4172
13  com.apple.AppKit             	0x93600090 -[NSApplication run] + 508
14  com.apple.AppKit             	0x936f08bc NSApplicationMain + 452
15  com.apple.myCocoaApplication 	0x00002138 _start + 344 (crt.c:272)
16  com.apple.myCocoaApplication 	0x00001fdc start + 60

Thread 0 crashed with PPC Thread State 64:
  srr0: 0x00000000fffeff18 srr1: 0x000000000200f030                        vrsave: 0x0000000000000000
    cr: 0x44004228          xer: 0x0000000000000004   lr: 0x0000000090742618  ctr: 0x00000000fffeff00
    r0: 0x0000000090742618   r1: 0x00000000bfffe750   r2: 0x000000006d656e75   r3: 0x00000000003535a0
    r4: 0x0000000090a18ab4   r5: 0x00000000bfffe830   r6: 0x00000000ffffffff   r7: 0x000000000034b5d0
    r8: 0x0000000000000158   r9: 0x00000000a0731844  r10: 0x0000000000000081  r11: 0x000000006f5f8ab4
   r12: 0x000000006d656e75  r13: 0x00000000a362e7c0  r14: 0x0000000000000100  r15: 0x0000000000000000
   r16: 0x00000000a361e75c  r17: 0x00000000a362e75c  r18: 0x00000000a362e75c  r19: 0x00000000a363e75c
   r20: 0x0000000000000000  r21: 0x0000000000000000  r22: 0x00000000a362e75c  r23: 0x0000000000354be0
   r24: 0x00000000a3634aa4  r25: 0x000000000034b5d0  r26: 0x0000000000000000  r27: 0x0000000090802464
   r28: 0x0000000000000003  r29: 0x00000000bfffe830  r30: 0x00000000003535a0  r31: 0x0000000090742464



After this a long Binary Images Description followes, which is not very useful, I think!
Perhaps the problem is, that I connected an action (setZero:) to the NSSwitchButton. When I check it: no crash, but when I uncheck it, then crash. But only when all images were displayed and the app has begun to display the images from the beginning.
I changed index=0; to index=[NSNumber numberWithInt:0]; Now it crashes at the line:

    
if([index intValue]==26 && [checkBox state]==1)
	{
		index=[NSNumber numberWithInt:0];
		index = [NSNumber numberWithInt:[index intValue]-1];
		[BildFenster setImage:[NSImage imageNamed:@"ende"]];
	}


Before the change it crashed when I unchecked the NSSwitchButton. I cant see where the problem could be! Perhaps it is forbidden for an integer value make 0-1=-1. But, in fact -1 is also an integer!

----

The above poster pointed out what is likely the problem. If you're in Cocoa, it's probably better for you to start out just working with pure NSNumbers in all cases. See the code below.

    
- (IBAction)setZero:(id)sender
{
	[index release];
       index = [[NSNumber numberWithInt:0] retain];
}


Don't forget to change your other methods (like init - the line should read     index = [[NSNumber numberWithInt:0] retain]; ). When setting     index, you want to release the old NSNumber object, then create (and retain) a new one. You should also be releasing     index immediately (release versus autorelease) when setting it to another value. By telling it to autorelease then setting it to a new value, you'll end up getting your new NSNumber released at some point in the future (like the next time your app is idle after handling the event that triggered this mistaken code). 

----
But when I release index before I set it to a new one, the increment of +1 is useless, or not? Did I understand you correctly when I say when I want to release index immediately I have to use autorelease. That means when a block is executed index is lost. But when I say retain index? How long survive index?
Is this correct, when I write the code below in my init-method?

    

index=[[NSNumber numberWithInt:0] retain];
		[index autorelease];
		index = [[NSNumber numberWithInt:[index intValue]-1] retain];


*You really need to take a step back and go over the difference between primitive types, pointers and memory management. That's why you're having trouble here, and they really are important things to understand before you can write working code. In the case of the example above, all you're getting the same object as if you had said index = [[NSNumber numberWithInt:-1] retain]*
----
Thanks at all for your help! I have applied very strong the retain and release rules and now it works. But I have to admit, that it would be good for me to read stuff about Cocoa and memory management. That was strange, that I had to always say release and retain. But, I think, that this has also many advantages. Thanks again! Now I understand better the memory management in Cocoa! Signed: Ludwig;-)

... and no, I meant the opposite. You must     release immediately. The     autorelease call will have it released 'at some point in the future'. Please re-read my post; that part is quite clear. I agree with the above poster but I'm going to be a bit more general. Re-read the Objective-C introduction (included in the documentation that comes with Developer Tools), then re-read the Cocoa introduction. You're missing a whole lot of very necessary fundamentals here. You'll do yourself and the community a world of good to go back to the basics and make sure you understand them first. These really are the first steps you should take.

*Also, don't use NSNumber when a simple int or float will do.*

----

Why you say now, I shall use simple int, when you said before, that I should use pure NSNumbers? Perhaps it's not the same poster? One poster said, that I cant mix primitive numbers and pointers to an object variable!

----

Yes, there are multiple posters. I posted that last advice, about using an int or float instead of NSNumber.

You're misunderstanding the advice. For any given variable, it is either an NSNumber or it is an int. You *must not* mix them. For example, this is an error:

    
NSNumber *x;
x = 42;


This is also an error:

    
int x;
x = [NSNumber numberWithInt:42];


In your code you're mixing them up, and that's why that poster said you can't mix them. It's true, you can't. There is absolutely no automatic conversion that goes on when you assign between them, and that will cause massive problems if you try to switch back and forth.

My advice was not to mix them. My advice was to forget about NSNumber altogether. There's absolutely no reason to use it here, and using an int will make your life a thousand times easier.

The *only* reason to ever use NSNumber is when you have a raw number value that you need to put in a place where you can only store objects, such as in an NSArray or NSDictionary. If you aren't in a situation like that, there's no reason not to use primitive types.
----

And what's about that:

    
antwort = [NSString stringWithFormat:@"Bild%@", index];


Here I cant use primitives instead of index, I think!

*Of course you can. Read the man page for printf (    man 3 printf in Terminal). If index is an int, you use the     %d format specifier instead of     %@.*

Another poster here - may I make a suggestion? Instead of making assumptions about what you can and can't do, how about you pick up a Cocoa and/or Objective-C book and read it? These are all **very basic** beginner issues and if you would take the time to properly educate yourself, you'd be a lot better off. But most especially don't keep making assumptions. This page is now way off topic, which is a very good indication that you are forgetting to **search the site for this information**. If you don't know how to use something, you should first research it, then if you still need help, post on the **relevant page**, don't append it to a page that has nothing to do with your question.

----

OK! I understand you!

*Glad to hear it! Discussion retired!*

