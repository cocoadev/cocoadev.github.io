---
layout: page
title: EncodingNSRectAndNSPointInAKeyedArchiver
---



I'm trying to encode NSRects and NSPoints via a Keyed Archiver, but I'm not sure if there's a solution to this. I tried to wrap them in NSValues, then encode them via encodeObject: forKey:. I got the following error: -[NSKeyedArchiver encodeValueOfObjCType:at:]: this archiver cannot encode structs.

How do you encode NSRects and NSPoints with their data in a keyed archiver?

----

    
// Specific
[encoder encodePoint:point forKey:@"point"]; // or encodePoint: for unkeyed archivers
[encoder encodeRect:rect forKey:@"rect"]; // or encodeRect: for unkeyed archivers

// General -- keyed archivers only (roughly the same for NSRect)
[encoder encodeFloat:point.x forKey:@"point.x"];
[encoder encodeFloat:point.y forKey:@"point.y"];

// General -- unkeyed archivers only (roughly the same for NSRect)
[encoder encodeValueOfObjCType:@encode(float) at:&point.x];
[encoder encodeValueOfObjCType:@encode(float) at:&point.y];

// Struct encoding -- unkeyed archivers only, unfortunately
// Maybe this is less safe than it looks
[encoder encodeValueOfObjCType:@encode(NSPoint) at:&point];
[encoder encodeValueOfObjCType:@encode(NSRect) at:&rect];

Of these four methods, the first is probably the best for your particular situation, but the second is probably the best in general. The other two only work with unkeyed coders, which is the only major problem with the third method. The last method may "look" best, and can encode any struct, but will not store object structure with more than two levels of indirection. Example:
    
struct InnerStruct {
    char *stringData;
}

struct EncodeStruct {
    struct InnerStruct *subStructure;
    int i;
}

*...*
    struct EncodeStruct *structToEncode;
    [encoder encodeValueOfObjCType:@encode(EncodeStruct *) at:&structToEncode];
    // The data in InnerStruct will not necessarily be encoded...so watch out.
*...*

So overall, it's up to you which method to choose here. All four should work. --JediKnil

----
I just read this wiki because I ran into similar problem. I had an NSMutableArray containing NSRect's wrapped in NSValue. Of course there is no easy way to write out these rects using encodeRect:forKey. So I solved it using a NSString representation like this:

    
NSMutableArray* rectStrings = [[NSMutableArray alloc] 
                                    initWithCapacity:[frameRects count]];
    NSEnumerator* e = [frameRects objectEnumerator];
    NSValue* value;
    
    while (value = [e nextObject]) {
        [rectStrings addObject:NSStringFromRect([value rectValue])];
    }
        
    [coder encodeObject:rectStrings forKey:@"frameRects"]; 


Hope this is of help to someone. Maybe somebody know a better way of doing this. Erik

----
I think this is a better way. Apple provides the following:

NSRect NSRectFromString(NSString *aString)

NSString *NSStringFromRect(NSRect aRect)

They are almost impossible to find in the docs!!

Please excuse me if I edited the wiki incorrectly, I'm wiki-dumb

-Paul Bruneau

