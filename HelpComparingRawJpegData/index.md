---
layout: page
title: HelpComparingRawJpegData
---

I am writing some software for work that edits jpeg IPTC data and I've run into a bit of an issue near the end here. (I've had lots of issues, but this one is really "hexing" me.

I feel like the solution should be simple, and that it's just me forgetting my C/C++, so I thought this would be a good place to ask.

Here is the issue:

When a folder of images is opened my application checks each files thumbnail via **[[NSWorkspace sharedWorkspace] iconForFile:aPath]** to see if it has one or not, and if not, it creates one and stores it.

This works great in almost every instance, that is except for (so far anyway) special case Photoshop documents. Photoshop in 2 different cases stores images with default non represenetive "default" style thumbnails instead of actual thumbnails. One case is "save for web" and the other one is something I believe can occur under certain preference sets (I've got both examples from our picturedesk team).

When I compare an image against a "bad image" I've saved in this manner: 
    
NSImage *thumbImage = [[NSWorkspace sharedWorkspace] iconForFile:aPath];
NSImage *BadImage = [[NSWorkspace sharedWorkspace] iconForFile:@"/Users/me/Desktop/BADJPEG.jpg"];

NSData *imageTypeData = [NSData dataWithBytes: [ [NSData dataWithData:[BadImage TIFFRepresentation]] bytes] length:104];
NSData *imageData = [NSData dataWithBytes: [ [NSData dataWithData:[thumbImage TIFFRepresentation]] bytes] length:104];   

if([imageData isEqualToData:imageTypeData]){execute Code here;}

it works fine.

However, if I try to do it in this manner: 

    
const char * imageBytes = "0x609280a0ad0701000060e01678e5020000f00200200000000000000000000200609280a08c0701000e44617272656e20576869746c657900000000000000000040e72aa3000000000000000000000000000000000000000070ab3d00040000000000000000000000";

NSData *imageTypeData = [NSData dataWithBytes:imageBytes length:104];


They never match up.

My question is (I believe) how do I get the bytes out of the NSData object that results from taking the first 104 bytes of an Jpegs TIFF Representation and then represent that in a constant NSData object that can be used to compare images without having to reference an actual Jpeg file with the same "malaise"

Any help would be much appreciated, I"m really hoping it's me just not doing some mundane thing correctly.

Thanks,
Nick

----

Umm... It's not working because you have a misunderstanding of hexadecimal numbers and strings in C...

const char * bytes = "0xDEADBEEF..." doesn't give you a block of memory containing the hex 0xDEADBEEF... It gives you back a chunk of memory with the ASCII codes for 'D', 'E', 'A', 'D', etc. which is definitely not the same thing. Fix the way you're building your array of bytes and it will work as you expect it to.

However, I'm wondering why in the world you are trying to hard-code such a long string of bytes in your program anyway. You say that you want to "get the bytes out of the NSData object that results from taking... etc." If you want bytes from an NSData object, just send it -bytes (like you already did earlier...). I'm probably just not grokking what it is you're trying to do.

----

The reason I want to hard code in ugly Hex/byte data is because there are only a finite number of "bad images" that I need to search for. So far I've only found 2. I feel that putting in the data of the first, 100-200 bytes of the bad style thumbnails (bad meaning those that must be generated) into the program is better than compiling bad jpeg examples into the application.

Here is what I'm trying to do in a nutshell, maybe it will make more sense:

Get the thumbnail from a jpeg via  **[[NSWorkspace sharedWorkspace] iconForFile:aPath]** and then get it's TIFF Representation as an NSData object.

I want that resulting NSData (just the first 104 bytes or so should be sufficient as any jpeg that is different will have those differences show up by then) to be accessable without having to scan in that jpeg again, ie I want to get the resulting data from that initial scan and use it as a constant so it isn't dependant on the example jpeg in the future.

Basically how do I get an NSData object from a TIFF Representation and get it's data so that it can be used as a constant without ever having to access said jpeg again.

Thanks so much for your help, I know I am using C strings and hex data wrong, I'm just pasting an example of one of my numerous attempts at getting this to work, and humbly understand that I'm going about it the wrong way.

----

Could you just write the those first 104 bytes to a file and just stick that in your executable bundle? It won't use any more space than it would if its in the application binary itself...

----

I did the above and just used NSBundle to access the small text files. That is a method I'm happy with. Thanks everyone for your help!

