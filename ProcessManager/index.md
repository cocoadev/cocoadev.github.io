---
layout: page
title: ProcessManager
---



ProcessManager is the Carbon API for, well, process management, though the most common use is merely to retrieve information about processes for other uses. Since 10.2 it has been part of the HIServices framework under ApplicationServices; before that you actually had to link to Carbon to use it.

Most of ProcessManager's API depends on being able to retrieve the "process serial number" of the process you're looking for. The best way of doing this uses ProcessManager's own     GetNextProcess(), which cycles through all of the current user's processes. Start at the beginning by passing in     {0, kNoProcess} as the current process serial number. Each call to     GetNextProcess() replaces the contents of its argument with the next process's serial number. When the function reaches the end of the list, it returns     procNotFound and puts     {0, kNoProcess} back into your serial number variable.

<pre>
NSDictionary *info;
BOOL foundApp = NO;
OSErr err;
ProcessSerialNumber psn = {0, kNoProcess};

while (!foundApp)
{	
    err = GetNextProcess(&psn);

    if (!err)
    {
        info = (NSDictionary *)ProcessInformationCopyDictionary(&psn, kProcessDictionaryIncludeAllInformationMask);
        foundApp = [@"com.apple.dock" isEqual:[info objectForKey:(NSString *)kCFBundleIdentifierKey]];
        [info release];
    }
    else
    {
        break; // either a problem or the end of the list
    }
}
</pre>

NSWorkspace offers     -launchedApplications as a little sliver of ProcessManager functionality; the same information (and more) can be obtained by stepping through all of the processes using     GetNextProcess() and examining info with     ProcessInformationCopyDictionary() as shown above.     -launchedApplications also only lists applications, not other processes or UI elements (see LSUIElement). Both     -launchedApplications and     GetNextProcess() only return information for the current user's processes.

----

When you receive application-related NSWorkspace notifications, they contain the application's process serial number within the notification's user info dictionary. You can convert it to a     ProcessSerialNumber struct using the following code:

<pre>
// Assuming the dictionary is called userInfo:
ProcessSerialNumber psn;
psn.lowLongOfPSN = [[userInfo objectForKey:@"NSApplicationProcessSerialNumberLow"] unsignedLongValue];
psn.highLongOfPSN = [[userInfo objectForKey:@"NSApplicationProcessSerialNumberHigh"] unsignedLongValue];
</pre>

