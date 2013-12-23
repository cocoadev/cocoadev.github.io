---
layout: page
title: QuestionforiPod
---



*Sorry, "iPod" isn't taking questions at this time ... Move along please.*

I have several questions about the iPod and Cocoa.

How does an app like PodWorks (http://www.scifihifi.com/podworks/ ) get a listing of all of the songs on the iPod with the ID3 tags? (I know that the songs are in the invisible iPod_Control folder, but they are scattered all around in there)

How I can I use the ListofConnectediPods code here to make a selected iPod unmount?

-scott (http://chs.claremont.edu/~ssmith )

----

To unmount: Get the iPod Volume and use [NSWorkspace unmountAndEjectDeviceAtPath:];

As to the songs on the iPod, you can read the entire structure in quite easily.


----

Thanks, but how do you get all of the song files to show up in a table?

-scott

--

Place them in a dictionary or array and then put them in an NSTableView.

--

Yes, but the songs are in many folders like F00, F01, etc. How would you pull out just those inside?

----

Have you looked at NSFileManager? Its got methods for listing the contents of a folder. Simply get the contents and add them to an array/dictionary and you are off.

----

As it happens, I've recently written a class that will parse the iTunesDB file on an iPod. This file contains an index of all the songs, including ID3 information. It doesn't retrieve playlists, though. I have put it up on my iDisk. You can download it from:

*http://idisk.mac.com/d.j.v.-Public/iTunesDBParser (using Finder's "Connect to Server..." command)
*http://homepage.mac.com/d.j.v./FileSharing3.html

-- DustinVoss

----

Nice. I'll give it a shot!      -scott

----

Well, I tried it, here is my code for the table:
    
- (int)numberOfRowsInTableView:(NSTableView *)tableView

{
NSMutableArray *songs = [NSMutableArray arrayWithCapacity:lastSongCount];
	NSEnumerator *songEnum = [self songEnumerator];
	NSDictionary *songDict; while ( (songDict = [songEnum nextObject]) != nil)
    
for (songEnum=0;songEnum<[songDict count];)
{
    [songs addObject:[songDict objectAtIndex:songEnum]];        
}
    return [songs count];
    [tableView setNeedsDisplay:YES];
}


- (id)tableView:(NSTableView *)tableView
      objectValueForTableColumn:(NSTableColumn *)tableColumn
      row:(int)row
{
NSMutableArray *songs = [NSMutableArray arrayWithCapacity:lastSongCount];
	NSEnumerator *songEnum = [self songEnumerator];
	NSDictionary *songDict; while ( (songDict = [songEnum nextObject]) != nil)
    
for (songEnum=0;songEnum<[songDict count];)
   { 
    [songs addObject:[songDict objectAtIndex:songEnum]];
       
    }
    return [songs objectAtIndex:row];
    [tableView setNeedsDisplay:YES];
}



When I run this, it builds all the way but then the app crashes right away with this error:

PodTool has exited due to signal 10 (SIGBUS).

I think that is a memory leak, but I am not sure. Anyone have any ideas for this?

-scott

----

Is that a straight copy-and-paste from what you ran?  I don't think that'd compile without a whole slew of warnings at least.  You're calling -objectAtIndex:, an NSArray method, on an NSDictionary and comparing an NSEnumerator* (songEnum) to an unsigned int ([songDict count]), both of which should produce compiler warnings at least.  

Also, there doesn't seem to be any way that the for loop will ever terminate since songEnum is never advanced within it, the -setNeedsDisplay: call (which is unnecessary anyways) won't be called because it's after the return statement and you should really consider setting up the array beforehand (in your -init method perhaps) rather than recreating it every time the data source methods get called (these methods get called many times).  -- Bo

----

Any suggestions as to how the code should look? And yes, I got many warnings during build time. -scott

It's a good idea to heed compiler warnings.  They're usually pointing out something the compiler thinks might be wrong.

----

Not really, since I don't know either how your data is stored in your data source or how your table is setup in your nib.  However, it's good general practice to go through any warnings you get one by one and either fix each one or at the very least make sure you understand why you're getting each one.  For more general info on tables, the NSTableViewTutorial page is an article on how to set up a table and its data source; it even uses an iPod theme for the example code.  -- Bo

----

I actually used that iPod code as a starting point in this project, but as you can see, the code didn't come over well for finding the songs.   -scott


----

Yeah, I got all of my errors out. I am not completley sure how well it will work, but we'll see. There is another problem though. In the header file I have:

#import <Foundation/Foundation.h>
#import <Cocoa/Cocoa.h>
#import <AppKit/AppKit.h>

Which would seem fine, but there are many errors with this for some reason. For each of the three, I get a whole set of warnings like this:


iTunesDBParser.h:13: warning: could not use precompiled header '/System/Library/Frameworks/Foundation.framework/Headers/Foundation-gcc3.p', because:

iTunesDBParser.h:13: warning: 'Foundation/NSObjCRuntime.h' has different date than in precomp

iTunesDBParser.h:13: warning: 'Foundation/NSArchiver.h' has different date than in precomp

iTunesDBParser.h:13: warning: 'Foundation/NSCoder.h' has different date than in precomp

iTunesDBParser.h:13: warning: 'Foundation/NSObject.h' has different date than in precomp

iTunesDBParser.h:13: warning:   and others...



What is up with that? And that slew of warnings comes in three times for each of Foundation, Cocoa, and AppKit. Other than that, no other warnings.

-scott

----

You don't need to import all 3; Importing Cocoa is fine since it imports Foundation and Appkit for you.  

These warnings are because Foundation's precompiled header is out of date; go to the terminal and type 'sudo fixPrecomps' to fix it.  -- Bo.

----

As I am using that code in a project right now, I can safely say it works, although I have tweaked it heavily to make it more efficient cpu and memory wise. Its very nice code though. -- MatPeterson

----

Thanks everyone. Mat, what did you change to make it more efficient? Oh, but if I run that in the Terminal, it says it could not be found!

-scott

----

Did you take the quotes off?  -- Bo

----

Well, it worked now for some reason. But it did it.


-scott


----

This is really strange. I have fix all of the warnings, and there are none, but it still crashes with the signal 10 error!!!

-scott

----

Well, did you fix that problem with the for loop I mentioned before?  While compiler won't catch that, it'll sure as hell crash your program. -- Bo


----

I did not, and I honestly don't know what to do for that.

-scott

You may want to get one of the CocoaBooks, which covers the basics, like using an enumerator.  The NSEnumerator documentation includes a sample enumeration loop.

----

Ok, I just looked at the iTuneDBParser class.  If you set up an instance of that  class in your object's initialization method (which I'll call itParser in the code below) and set your column identifiers to match the keys listed in the parser's header (e.g. "title" for the song name) then your code becomes:

    
#pragma mark -
#pragma mark TABLE DATA SOURCE
- (int)numberOfRowsInTableView:(NSTableView *)tableView
{
	return [itParser countSongs];
}
- (id)tableView:(NSTableView *)tableView objectValueForTableColumn:(NSTableColumn *)tableColumn row:(int)row
{
	return [itParser allSongs] objectAtIndex:row] objectForKey:[tableColumn identifier;
}


Doesn't that seem a lot easier than what you're doing?  -- Bo

----

That would seem easier and cleaner, but would the instance look like this:

    

- (int) itsParser
{
        NSMutableArray *songs = [[NSMutableArray alloc] init];
	NSEnumerator *songEnum = [self songEnumerator];
	NSArray *songDict;
        while ( (songDict = [songEnum nextObject]) != nil)
}



Then below that would be the code you put up? I think it is something like that.

-scott

----

I think I said that; itParser should point to an instance variable of class iTunesDBParser in your class's header, which you should allocate and initialize in your class's designated initializer.  If you find this terminology confusing, I would suggest reading Apple's ObjectiveC docs (at file:///Developer/Documentation/Cocoa/ObjectiveC/index.html on your computer) to bring yourself up to speed.   -- Bo

----

Guys, as the author of iTunesDBParser, I don't recommend using **allSongs** on a regular basis. All it does is run through the enumerator, which will read & parse the file from disk. I'd call it once and cache the result, and re-call **allSongs** only when you think the DB changed. -- DustinVoss

----

I've removed iTunesDBParser, as MatPeterson and I have come to an...*arrangement*. -- DustinVoss

----

Dustin, what about those who already downloaded your class? I emailed you about it - can I still use it? Or is it already in use in the latest iPodRip beta? -- KevinWojniak

If you've already downloaded it, go ahead and continue to use it. As for iPodRip, MatPeterson is the author, hence the arrangement. -- DustinVoss

