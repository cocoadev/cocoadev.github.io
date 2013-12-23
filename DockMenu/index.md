---
layout: page
title: DockMenu
---

Is it possible to add images to a dock menu like this?
    
NSMenuItem *item1 = [dockMenu addItemWithTitle:@"TEST ITEM"];
item1 = [dockMenu itemAtIndex:0];
[item1 setImage:[NSImage imageWithName:@"image1"]];


--JoshaChapmanDodson

----

Try it. If it works, the answer is "yes". If it doesn't work, the answer is "no".
Seriously, you'd think that people would try things before they posted their questions.

----

Dock menus don't support images in the menu items I don't believe.

-- KentSutherland

----

The list of open windows has a window image. -- > Which is probably internal Apple code. I highly doubt its possible to add images to dock menu items but I have not tried.

----

I think it is possible, but not from Cocoa -- the menu system seems to be Carbon with some Cocoa wrappers, which can only access half the functionality.

----

I haven't tried it, but in 10.3 NSMenuItem has     - (void)setAttributedTitle:(NSAttributedString *)string, which according to Apple *You can use this method to add styled text and embedded images to menu item strings*

----

So it seems that setting icons on NSMenuItem for a dock menu is futile. For my app, the lack of icons is sort of lame, so I plunged into Carbon, but it seems that I am totally not getting something, because even though the calls to get icon from files/apps succeed, the icons displayed are default icons (for apps it is showing the document icon). Any thoughts on what I am missing here?

    
...
  IconRef iconRef;
  SInt16 label;
  FSSpec spec;

  err = convertPathToFSSpec(fullpath, &spec);
  // I am passing things like /Applications/Some.app, 
  // but that doesn't seem to do the trick.
  // Passing the path of the actual executable doesn't
  // seem to do it either...
			
if(err == noErr)
{
  err = GetIconRefFromFile(&spec, &iconRef, NULL);
  if(err == noErr)
  {
    err = SetMenuItemIconHandle(gDockMenu
              , menuItem
              , kMenuSystemIconSelectorType
              , iconRef);

    ReleaseIconRef(iconRef);
  }
}
	

I have also tried:

    
NSImage *icon = [[NSWorkspace sharedWorkspace] iconForFile:fullpath];
[icon setSize:NSMakeSize(16,16)];
NSBitmapImageRep *bitmap = [NSBitmapImageRep imageRepWithData:
                [icon TIFFRepresentation]];
NSData *data = [bitmap representationUsingType:NSPNGFileType properties: nil];
NSString *iconFile = [NSString stringWithFormat: 
                                       @"/tmp/appmenuicons/%@.png", title];
[data writeToFile: iconFile atomically:YES];

NSURL *url = [NSURL fileURLWithPath:iconFile];
CGDataProviderRef provider = CGDataProviderCreateWithURL(url);
CGImageRef image = CGImageCreateWithPNGDataProvider(provider
                                          , NULL
                                          , false
                                          ,kCGRenderingIntentDefault);

NSLog(@"%d, %d\n", CGImageGetWidth(image), CGImageGetHeight(image));
// this prints 16x16, so the image loaded.

// specifying kMenuCGImageRefType doesn't show any icon
// but kMenuSystemIconSelectorType again defaults to the default document icon...
err = SetMenuItemIconHandle(gDockMenu, menuItem, kMenuSystemIconSelectorType, image);


CGDataProviderRelease(provider);
CGImageRelease(image);


Any thoughts?

/a
----
Your carbon code is wrong. In particular the line
    
err = SetMenuItemIconHandle(gDockMenu , menuItem, kMenuSystemIconSelectorType, iconRef);

Says that your parameter is a 4 char code, registered with creator  kSystemIconsCreator when it is in fact an IconRef. You get the default document icon because there is no registered icon with the 4 char code that matches the pointer value of your iconRef. Pass kMenuIconRefType instead.

----

Searching through my list archives I found this. Maybe someone will find it helpful.

*Arbitrary NSImages can't be passed across the app->Dock boundary. The only images that can be displayed in a Dock menu are IconRefs and images located in your app's bundle that are specified by name. I'm not sure if NSMenu supports either of these types of images (I do know they can be specified in Carbon).*

