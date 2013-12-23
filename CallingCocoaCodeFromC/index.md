---
layout: page
title: CallingCocoaCodeFromC
---

I have a C app that gets the "model" string from sysctl.

This code works fine:
    
char * model;
len = sizeof(model);
mib[1] = HW_MODEL;
	sysctl(mib, 2, NULL, &len, NULL, 0);
	model = malloc(len);
	sysctl(mib,2,model,&len,NULL,0);

leaving the correct string (e.g., "MacBookPro1,1") in the variable "model".

Now I have this Objective-C code that reads a plist whose keys are the strings like that above, and whose values are the human-readable model names (like "Power Macintosh G5"):
    
#import <Cocoa/Cocoa.h>

int main(int argc, char *argv[])
{
	NSAutoreleasePool *pool = [NSAutoreleasePool new];
	NSString *path = [[NSBundle mainBundle] pathForResource:@"Macintosh" ofType:@"plist"];
	NSDictionary *modelList = [NSDictionary dictionaryWithContentsOfFile:path];
	NSString *keyFound = [modelList valueForKey:@"PowerMac7,2"];
	NSLog(keyFound);
	[pool release];	
    return NSApplicationMain(argc,  (const char **) argv);
}

I want to call the Objective-C method from the C app, passing the "model" string and getting back the valueForKey: of the modelList.

I have searched all over, read my two Cocoa books, and tried these things:
- changing the suffix of the C source file to ".m" and adding #includes for <Cocoa/Cocoa.h> and <Foundation/Foundation.h>
- dropping the Objective-C code inline under the C code, minus the autoreleasePool statements
- trying to declare the Objective-C file external, but I am lost on how to do that.

Can anyone please point me to a reference for adding a few lines of Objective-C code to an existing C source code so that the Objective-C can be used to look up a value in a dictionary? I hate to think I have to convert this elegant Objective-C to a C array and walk through it just to get one value from a key.

I would sure appreciate any and all pointers on combining these two pieces of code so that the Obj-C can use the value in the C variable as a key for its dictionary.

THANKS!
----
The following code (typed in mail and never compiled) should compile just fine in a file that has the .m extension.
Objective-C is a proper superset of ANSI/ISO C and all legal C programs can be compiled as Objective-C, intermixed with Objective-C, called from Objective-C, etc.

    
int main(int argc, char *argv[])
{
   NSAutoreleasePool   *pool = [NSAutoreleasePool new];
   char                        *model;
   size_t                      len = sizeof(model);
   int                          mib[1] = HW_MODEL;

   sysctl(mib, 2, NULL, &len, NULL, 0);
   model = malloc(len);
   sysctl(mib,2,model,&len,NULL,0);

   NSString                   *path = [[NSBundle mainBundle] pathForResource:@"Macintosh" ofType:@"plist"];
   NSDictionary             *modelList = [NSDictionary dictionaryWithContentsOfFile:path];
   NSString                   *keyFound = [modelList valueForKey:[NSString stringWithCString:model]];
   NSLog(@"%@", keyFound);

   [pool release];
   free(model);
   model  = NULL;

   exit(0);
}	


Alternatively, do the following in one source file or multiple source files:
    
static NSString *RetrieveHardwareModelID()
{
   char                        *model;
   size_t                      len = sizeof(model);
   int                          mib[1] = HW_MODEL;
 
   sysctl(mib, 2, NULL, &len, NULL, 0);
   model = malloc(len);
   sysctl(mib,2,model,&len,NULL,0);
   NSString                  *result = [NSString stringWithCString:model];
   free(model);
   model  = NULL;

  return result;
}


int main(int argc, char *argv[])
{
   NSAutoreleasePool    *pool = [NSAutoreleasePool new];
   NSString                   *path = [[NSBundle mainBundle] pathForResource:@"Macintosh" ofType:@"plist"];
   NSDictionary             *modelList = [NSDictionary dictionaryWithContentsOfFile:path];
   NSString                   *keyFound = [modelList valueForKey:RetrieveHardwareModelID()];
   NSLog(@"%@", keyFound);

   [pool release];

   exit(0);
}	


Or if the main program is C, how about the following:

    
static void LogHumanReadableHardwareModelForHWID(const char *aHWID)
{
   NSAutoreleasePool    *pool = [NSAutoreleasePool new];
   NSString                   *path = [[NSBundle mainBundle] pathForResource:@"Macintosh" ofType:@"plist"];
   NSDictionary             *modelList = [NSDictionary dictionaryWithContentsOfFile:path];
   NSString                   *keyFound = [modelList valueForKey:[NSString stringWithCString:aHWID]];
   NSLog(@"%@", keyFound);

   [pool release];
}

int main(int argc, char *argv[])
{
   char                        *model;
   size_t                      len = sizeof(model);
   int                          mib[1] = HW_MODEL;
 
   sysctl(mib, 2, NULL, &len, NULL, 0);
   model = malloc(len);
   sysctl(mib,2,model,&len,NULL,0);

   LogHumanReadableHardwareModelForHWID(model);

   free(model);
   model  = NULL;

   exit(0);
}


----
----

First of all, I don't know why this doesn't work.  But, you could just use Core Foundation/CFDictionary from C to do what you want, there are analogs to all these calls that have a C API and you don't have to link in and start up foundation or cocoa to do it.  (By the way, all the calls you are using are in Foundation and not Cocoa, so you could just be using that.)

----
----

I found this somewhere on the web, I don't remember where:

Mixing C functions in Obj-C classes
?This feature is great, and often needed. You can mix Objective-C style methods with C-style functions in your code, meaning you can do crucial CPU-intensive stuff without the overhead of Cocoa's messaging. Everything seems to work out as you'd expect, except for the rather obvious problem that C isn't object orientated. So whilst it's valid to write Objective-C code inside a C function, unless you can get a reference to the receiving object, that Objective-C syntax statement won't work, and typically that boils down to getting a reference for self, since once you've got that you can do pretty much everything else. The trick is to make sure that whenever you enter a C function from an Obj-C method, pass in self as a void * (or the actual class if you wish). Where callbacks are in effect (for example, that pesky ioProc function if you're doing CoreAudio stuff), use the void *userData and then cast it to the proper class. That way, you can:

    
void myC_function(int a, void *userData) 
{
myClass *self = (myClass *)userData;
[self setMyInteger: a];
NSLog(@"My integer is set to %d",[self myInteger]);
}


----
And don't forget to add "-framework Foundation" to the gcc invocation (possibly by adding Foundation.framework in the Xcode project) ;-) unless you go with CoreFoundation...

----

Thanks to all. I just redid the app as a Foundation Tool and linked only against Foundation.h.
Now my Objective-C code runs fine. I still get this warning
warning: assignment discards qualifiers from pointer target type
    
NSString *lookupString = [NSString stringWithCString:model encoding:1];
NSBundle *myBundle = [NSBundle mainBundle];
NSString *path = [myBundle pathForResource:@"Macintosh" ofType:@"plist"];
NSDictionary *modelList = [NSDictionary dictionaryWithContentsOfFile:path];
	
NSString *keyFound = [modelList valueForKey: lookupString];
modelName = [keyFound cStringUsingEncoding:1]; //<=======Warning Here

modelName is declared thusly

    
char *machine, *model, *modelName;


But that is a minor point - ADC didn't have an answer for me and I am just curious as to if it is possible to fix that.

Now - does anyone know why the build with the Debug configuration would all of a sudden execute (not compile; execute) 5 seconds faster than the Release build? I have checked all the build settings and I must be missing something somewhere. It did not do this previously - in fact the execution speeds seem to be reversed from what they should be - I have the Debug symbols on in the Debug build, and ZeroLink. I have NO optimization in the Release build - that is by design as this is a type of benchmark.

Thanks

----

That warning is because cStringUsingEncoding returns const char *, not just a char *.  The const is the qualifier that you are discarding and that has the compiler worried.  Just make the type const char * and the warning will go away.  This is done because you can't modify the contents of the string it is giving you.

As for your launch time of the app, see if ZeroLink is doing it (ie:  turn it off and see what happens).  I am surprised that the difference would be that big but if you use ZeroLink then the entry point is much smaller and the initial launch would appear to be slightly faster.

--JeffDisher

