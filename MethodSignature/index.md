---
layout: page
title: MethodSignature
---



A method signature is the type information for the SeLector arguments and return value as well as the number of arguments. If several methods have matching return types and argument types and numbers, their signatures match. This is useful since some methods expect you to pass a SeLector as an argument with the stipulation that it match a certain signature, for instance, the sheet methods expect to be supplied with a SeLector to handle the sheet closing which has the following signature:

    
- (void)sheetDidEnd:(NSWindow *)sheet
         returnCode:(int)returnCode
        contextInfo:(void *)contextInfo;


So the sheet will be happy if I pass:

    
- (void)myFancySheetDidEnd:(NSPanel *)myPanel
                      code:(int)code
                      info:(void *)info;


The signature is void return and 3 args of types (in order) window object, int and pointer to void.

----

A selector is not actually required to have text (*I believe the correct term is label?*) before each colon. Try compiling the following:
    
// in header
- :x :y :z;

// in code
[obj :nil :nil :nil];

It will work fine. It's probably not a good idea to do this kind of thing because of readability, etc., but it's not illegal.

*In fact if you look at old NeXT step code you'll notice it was always (well almost always) done this way*

