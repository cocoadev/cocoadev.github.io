---
layout: page
title: QuicktimeSupportMediaFileExtensions
---



Hello,

Is there any way to get all supported Quicktime media extensions like .mov .mp3 and so on?

Thank you

----

Yep. I don't have sample code right now, but maybe you can find some on http://developer.apple.com/ . The idea is to enumerate the list of open QuickTime components and ask for their supported file types and/or extensions. Something like this:

    
Component component = NULL;
ComponentDescription looking;
NSCharacterSet *spaceSet = [NSCharacterSet characterSetWithCharactersInString:@" "];

looking.componentType = MovieImportType;
looking.componentSubType = 0; // Any subtype is OK
looking.componentManufacturer = 0; // Any manufacturer is OK
looking.componentFlags = movieImportSubTypeIsFileExtension;
looking.componentFlagsMask = movieImportSubTypeIsFileExtension;

while (component = FindNextComponent(component, &looking)) {
    ComponentDescription description;
    
    if (GetComponentInfo(component, &description, NULL, NULL, NULL) == noErr) {
        // the extension is present in the description.componentSubType field, which really holds
        // a 32-bit number. you need to convert that to a string, and trim off any trailing spaces.
        // here's a quickie...
        char ext[5] = {0};
        NSString *extension;

        bcopy(&description.componentSubType, ext, 4);

        extension = [[NSString stringWithCString:ext] stringByTrimmingCharactersInSet:spaceSet];

        // do something with extension here ...    
    }
}


If you get this working, please post better sample code. Thanks!

-- MikeTrent
----

Thanks Mike,

It really works!

