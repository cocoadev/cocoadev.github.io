---
layout: page
title: NSBrowserIcons
---



Here's a method to construct NSBrowser friendly icons.  --zootbobbalu

Just pass this method a path to a safe location so a new folder can be created and filled with tiff images for all of the system icons. These TIFFs are sized to create an NSBrowser that looks close to the finders column view layout. 

(Note - the SystemIcons.bundle seems to have changed to CoreTypes.bundle in Tiger or at some earlier time...)

    
- (BOOL)constructFinderIconsAtPath:(NSString *)path {
    NSFileManager *manager = [NSFileManager defaultManager];
    NSString *iconPath = @"/System/Library/CoreServices/SystemIcons.bundle/Contents/Resources";
    NSArray *files = [manager directoryContentsAtPath:iconPath];
    NSEnumerator *fileEnum = [files objectEnumerator]; NSString *file;
    NSImage *imageBuffer = [[[NSImage alloc] initWithSize:NSMakeSize(24, 16)] autorelease];
    [manager changeCurrentDirectoryPath:iconPath];
    NSImage *smallBuffer = [[[NSImage alloc] initWithSize:NSMakeSize(14, 14)] autorelease];
    BOOL isDir;
    if (![manager fileExistsAtPath:path isDirectory:&isDir]) {
        if (![manager createDirectoryAtPath:path attributes:nil]) return NO;
    }
    else if (!isDir) return NO;
    while (file = [fileEnum nextObject]) {
        NSImage *image = [[[NSImage alloc] initWithContentsOfFile:file] autorelease];
        NSArray *reps = [image representations];
        NSEnumerator *repEnum = [reps objectEnumerator]; NSImageRep *rep;
        while (rep = [repEnum nextObject]) {
            NSSize size = [rep size];
            if (NSEqualSizes(size, NSMakeSize(16, 16))) {
                [smallBuffer addRepresentation:rep];
                [smallBuffer setScalesWhenResized:YES];
                [smallBuffer setSize:NSMakeSize(15, 15)];
                [imageBuffer lockFocus];
                [smallBuffer drawAtPoint:NSMakePoint(5, 0) fromRect:NSMakeRect(0, 0, 15, 15) 
                            operation:NSCompositeCopy fraction:1.0f];
                [imageBuffer unlockFocus];
                NSData *tiff = [imageBuffer TIFFRepresentation];
                NSString *writePath = [file stringByDeletingPathExtension];
                writePath = [NSString stringWithFormat:@"%@/%@.tiff", path, writePath];
                if ([tiff writeToFile:writePath atomically:YES]) {
                    NSLog(@"new icon at path: %@", writePath);
                }
                [smallBuffer removeRepresentation:rep];
            }
        }
    }
    return YES;
}


----

OK this is awesome *just* what I needed, except I don't need the images as files since my images are created on the fly (using NSWorkspace's iconForFileType). I just edited it so it returns the resized image for an image that is passed to it: --KevinWojniak

    
- (NSImage *)browserIconForImage:(NSImage *)image {
    NSImage *imageBuffer = [[[NSImage alloc] initWithSize:NSMakeSize(24, 16)] autorelease];
    NSImage *smallBuffer = [[[NSImage alloc] initWithSize:NSMakeSize(14, 14)] autorelease];

    NSArray *reps = [image representations];
    NSEnumerator *repEnum = [reps objectEnumerator];
    NSImageRep *rep;
    while (rep = [repEnum nextObject]) {
        NSSize size = [rep size];
        if (NSEqualSizes(size, NSMakeSize(16, 16))) {
            [smallBuffer addRepresentation:rep];
            [smallBuffer setScalesWhenResized:YES];
            [smallBuffer setSize:NSMakeSize(15, 15)];
            [imageBuffer lockFocus];
            [smallBuffer drawAtPoint:NSMakePoint(5, 0) fromRect:NSMakeRect(0, 0, 15, 15) 
                            operation:NSCompositeCopy fraction:1.0f];
            [imageBuffer unlockFocus];
            
            [smallBuffer removeRepresentation:rep];

            return imageBuffer;
        }
    }
    return nil;
}


----
This is what I'm looking for but it seems like a lot of code just to add icons, i tried both of them i couldn't get them to work.

----
Ditto to the person above - I can't figure out exactly how to use this code.

----

I posted this a long time ago. Sorry for the hack job. I now draw icons the Finder way.


*Create a new Cocoa Application Project named IconApp
*Open the MainMenu.nib file in IB
*Add an image view to the main window
*Subclass NSObject and name the new class IconController
*Instantiate IconController and then add an outlet named "imageView"
*Connect the "imageView" outlet from the controller to the NSImageView in the main window
*Create files for IconController
*Save "MainMenu.nib"
*Edit "IconController.h" and "IconController.m" so that it looks like the code below
*Build and run


**IconController.h**

    
#import <Cocoa/Cocoa.h>

@interface IconController : NSObject
{
    IBOutlet id imageView;
}
@end

@interface XFIcon : NSObject {
	IconRef iconRef;
}
- (id)initWithIconForFile:(NSString *)file;
- (void)drawWithFrame:(NSRect)frame context:(CGContextRef)context;
@end




**IconController.m**

    
#import "IconController.h"

NSRect XFImageBoundsForFrame(NSRect fr, NSSize size) {
	float srcRatio = size.width / size.height, destRatio = NSWidth(fr) / NSHeight(fr);
    fr = NSInsetRect(fr, 1.0f, 1.0f);
	if (srcRatio > destRatio) 
		return NSInsetRect(fr, 0.0f, (NSHeight(fr) - NSWidth(fr) / srcRatio) / 2.0f);
	else 
		return NSInsetRect(fr, (NSWidth(fr) - NSHeight(fr) * srcRatio) / 2.0f, 0.0f);
}

@implementation XFIcon

- (id)initWithIconForFile:(NSString *)file {
	self = [super init];
	if (self) {
		FSRef ref;
		FSSpec fsSpec;
		SInt16 iconLabel;
		Boolean isDir;
		FSCatalogInfo catInfo;
		if (FSPathMakeRef((const UInt8 *)[file fileSystemRepresentation], &ref, &isDir)) goto ABORT;
		if (FSGetCatalogInfo(&ref, kFSCatInfoCreateDate, &catInfo, NULL, &fsSpec, NULL)) goto ABORT;
		if (GetIconRefFromFile(&fsSpec, &iconRef, &iconLabel)) goto ABORT;
	}
	return self;
	
ABORT:;
	[self release];
	return nil;
}
- (void)dealloc {
	if (iconRef) ReleaseIconRef(iconRef);
	[super dealloc];
}

- (void)drawWithFrame:(NSRect)frame context:(CGContextRef)context {
	frame = XFImageBoundsForFrame(frame, NSMakeSize(16.0f, 16.0f));
	PlotIconRefInContext(context, (CGRect *)&frame, nil, nil, nil, nil, iconRef);
}

@end


@implementation IconController
- (void)awakeFromNib {
	NSImage *image = [[[NSImage alloc] initWithSize:NSMakeSize(128.0f, 128.0f)] autorelease];
	NSString *file = @"/Applications/Address Book.app";
	XFIcon *icon = [[[XFIcon alloc] initWithIconForFile:file] autorelease];
	[image lockFocus];
	[icon drawWithFrame:NSMakeRect(0.0f, 0.0f, 128.0f, 128.0f) 
			context:[[NSGraphicsContext currentContext] graphicsPort]];
	[image unlockFocus];
	[imageView setImage:image];
}
@end




I spent all of about ten minutes typing this up without checking for errors, so please only use this as a starting point.

--zootbobbalu

*That     goto is pretty ugly...why can't you just nest your     if statements? Also, check out Apple's ImageAndTextCell class, part of the DragNDropOutlineView example. --JediKnil*

----

I don't think DragNDropOutlineView shows you how to obtain finder icons, but I could be wrong. I was just trying to give an example on how to get file icons. 

Is     goto inherently ugly? Personally I'm not neurotic about the goto statement. Using spotlight to search for the string "goto bail" I get 400+ hits in the source code for Darwin 8.2 (that's 400+ individual files containing "goto bail"). 

Spaghetti code is BAD!!! and this is why every text book and instructor will say to avoid using labels. So keeping a bad light on its use is fine by me. 

As a side note, the two rules I live by when using     goto are:


*never use more than one label per function
*only jump forward


I could nest two if statements, but then I would have two extra release/nil statements. I guess I'm living on the edge of ridicule!!

--zootbobbalu

*Using     goto to jump to cleanup code when an error occurs is a time-honored technique dating back to the early days of the Toolbox on the Mac, and probably earlier on other platforms. I see no reason for criticism here.*

Zoot, I followed the instructions, built it without errors, and the app just sits there. Can you give any more direction as to what to do with the app once it builds and runs? - mattybinks

----

Can you run/log this and tell me what you get?

    
@implementation IconController
- (void)awakeFromNib {
    NSImage *image = [[[NSImage alloc] initWithSize:NSMakeSize(128.0f, 128.0f)] autorelease];
    NSString *file = @"/Applications/Address Book.app";
    XFIcon *icon = [[[XFIcon alloc] initWithIconForFile:file] autorelease];
    [image lockFocus];
    [icon drawWithFrame:NSMakeRect(0.0f, 0.0f, 128.0f, 128.0f) 
			context:[[NSGraphicsContext currentContext] graphicsPort]];
    [image unlockFocus];
    [imageView setImage:image];
    NSLog(@"imageView: %@ image: %@ icon: %@", imageView, [imageView image], NSStringFromClass([icon class]));
}
@end



--zootbobbalu

----

    
[Session started at 2006-01-05 23:02:05 -0500.]
2006-01-05 23:02:07.504 IconApp[6113] imageView: (null) image: (null) icon: XFIcon


--endymion

----

    
@interface IconController : NSObject
{
    IBOutlet id imageView;
}
@end


Looks like the problem is you didn't make a connection between the IBOutlet "imageView" and an image view placed in the main window of the application. --zootbobbalu

----

Oh wow beautiful, it works!  Thanks for the help, I made a stupid beginner mistake trying to make the connection.  Initially I had the same problem as mattybinks but now I don't.  It displays the Address Book icon like it's supposed to.  Now I can pull this apart and figure out how it works.  Thanks for passing on some wisdom.

----

Hey Zoot, same here as above. I made the same connection error. Thanks so much! - mattybinks

