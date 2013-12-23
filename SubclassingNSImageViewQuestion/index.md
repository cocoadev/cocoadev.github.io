---
layout: page
title: SubclassingNSImageViewQuestion
---



I created a subclass of General/NSImageView - General/ImageViewObject, then set my onscreen's Image View's Custom Class to that class after reading it into General/ImageBuilder.

I am doing this because when cycling thru' a data set in General/CoreData, if I come across a record without an image, I want to load a default image from the Bundle.

I did something like this:

    

.h file:
 
#import <Cocoa/Cocoa.h>


@interface General/ImageViewObject : General/NSImageView {

}

@end



.m file:


#import "General/ImageViewObject.h"


@implementation General/ImageViewObject

-(void) setImage:( General/NSImage * ) newImage {
	[super setImage:newImage];
}

-(General/NSImage *) image {
	General/NSImage *img = [super image];
	if( nil == img ){
		img = General/[NSImage imageNamed:@"no_image"];
	}
	return img;
}
@end



I read the .h file into General/InterfaceBuilder, set the General/NSImageView's custom class to General/ImageViewObject, then recompiled everything.

Not only does this appear to not work, but further, if I stick logging messages in the two methods, I never see the log messages.

Am I missing something?

Thanks! --chornbe (see my ongoing learning of programming the Mac at http://learntomac.blogspot.com)

----

Have you set the class of your image view using the Custom Class panel in the Interface Builder inspector? This tells IB that the selected image view should use your custom class.

----

Yep, did so, using the same mechanisms I've done for setting other custom classes (apple-5 in the inspector, I believe?). I guess I should clarify, when I say "not working", it appears as tho' the subclass is never factored in at all - no logging messages, no break-points in debugging, etc. Very frustrating. --thx. (and thanks for the format help above)

I should also point out I did a work-around by doing in the data layer. When the data object returned from my General/NSManagedObject subclass is 0 bytes or nil, I then create up a new General/NSImage from my default image, then stuff that into an General/NSData object, and pass that back. It works, and an argument could be made that handling that in the data layer is the better way to go. 6 of one, 1/2 dozen of the other, I guess :-)

Thusly:

    

-(General/NSData *) pictureData {
//	General/NSLog( @"retriving picture data" );
	General/NSData *data = [self valueForKey:@"picture"];
	if( data == nil || [data length] < 1 ){
//		General/NSLog( @"creating new image from disk" );
		[data release];
		General/NSBundle *main = General/[NSBundle mainBundle];
		General/NSString *no_image_path = [main pathForImageResource:@"no_image"];
		[main release];
		General/NSLog( no_image_path );
		data = General/[[NSData alloc] initWithContentsOfFile:no_image_path ];
	}
	return data;
}


