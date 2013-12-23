---
layout: page
title: HowToSortAnArrayOfFilesByDate
---



Sorry, maybe it's a stupid question, but I can't figure out how to do that. I can use NSFileManager     enumeratorAtPath:, but how can I sort the resulting NSDirectoryEnumerator so to sort files by date? Can I use something like     sortedArrayUsingDescriptors: (which descriptor do i need then)?

----

Well, you can't sort enumerators directly. You can only sort arrays. So, before you get an enumerator from your array, if your array is holding strings that are full paths, you could do something ugly like this:
    
@implementation NSString (comparingPaths)
- (NSComparisonResult)comparePath:(NSString *)path
{
    NSFileManager *fm = [NSFileManager defaultManager];
    return [fm fileAttributesAtPath:self traverseLink:NO] objectForKey:[[NSFileModificationDate] compare:fm fileAttributesAtPath:path traverseLink:NO] objectForKey:[[NSFileModificationDate]];
}
@end

This isn't efficient or good, but just shows how you could quickly do it. You'd probably want to store the file info in a class and then sort an array of your classes.

