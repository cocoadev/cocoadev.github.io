---
layout: page
title: AllApplications
---

Is there an easy way to get an array listing all installed applications on the computer?

----

Get the directory contents of /Applications and ~/Applications and check either the Mac type for APPL or the bundle extension for .app

see also NSPathUtilities

----

There has to be some service for this because NSWorkspace provides a method to update Finder's database for installed applications     [[NSWorkspace sharedWorkspace] findApplications] Maybe CoreFoundation provides a function that returns an array of all installed applications. --zootbobbalu 

----

Perhaps, but even so, findApplications will only search the paths above anyway, AFAIK.

I just checked - findApplications searches /Applications, /Network/Applications and /Developer/Applications NOT ~/Applications

----

    [[NSWorkspace sharedWorkspace] findApplications] and anything it uses is not much different than searching standard application paths. 

----

Dynamically how can I provide option to use to open a file with a particular application?

    
#import <Cocoa/Cocoa.h>

void ApplicationsInDirectory(NSString *searchPath, NSMutableArray *applications) {
    BOOL isDir;
    NSFileManager *manager = [NSFileManager defaultManager];
    NSArray *files = [manager directoryContentsAtPath:searchPath];
    NSEnumerator *fileEnum = [files objectEnumerator]; NSString *file;
    while (file = [fileEnum nextObject]) {
        [manager changeCurrentDirectoryPath:searchPath];
        if ([manager fileExistsAtPath:file isDirectory:&isDir] && isDir) {
            NSString *fullpath = [searchPath stringByAppendingPathComponent:file];
            if (file pathExtension] isEqualToString:@"app"]) [applications addObject:fullpath];
            else [[ApplicationsInDirectory(fullpath, applications);
        }
    }
}

NSArray *AllApplications(NSArray *searchPaths) {
    NSMutableArray *applications = [NSMutableArray array];
    NSEnumerator *searchPathEnum = [searchPaths objectEnumerator]; NSString *path;
    while (path = [searchPathEnum nextObject]) ApplicationsInDirectory(path, applications);
    return ([applications count]) ? applications : nil;
}


int main(int argc, char *argv[]) {
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
    NSString *homeAppDir = @"~" stringByExpandingTildeInPath] stringByAppendingPathComponent:@"Applications"];
    [[NSArray *searchPaths = [NSArray arrayWithObjects:@"/Applications", @"/Network/Applications", 
                                                        @"/Developer/Applications", homeAppDir, nil];
    NSLog(@"start application search");
    NSArray *applications = AllApplications(searchPaths);
    NSLog(@"end application search\napplications: \n%@", applications);
    [pool release];
    return 0;
}


----

I noticed the search path "/Network/Applications" -- how would one set up such a situation? In other words, can anybody here tell me where to look in apple's documentation on how to set up such a situation?

----

How to set up Network-accessible apps? Man, I wish I knew. Probably something to do with MacOSXServer...

----

The search path "/Network/Application" is probably for a local network application server. I added it to this example for no special reason. --zootbobbalu

Perhaps, but I've heard of wider use in e.g. lab setups. I would *love* to try something like that, but can't afford three Macs and OS X Server, so it's not gonna happen. But I'd still like to serve my apps across the network to my Dad's machine. Hm.

----

**/Network/Applications**

I did some research on this and while I am not 100% positive (I have not spent a lot of time in front of Mac OS X Server), It would appear the Workgroup Manager on Mac OS X Server can limit what Applications a client machine executes (locally or remotely) as well as limiting the ability to see the local drive to begin with. So, I suspect if an Application is *approved*, it becomes available in an Applications folder along with the other /Network entities?

----

See topic ServingTheNetworkDirectory for details

----

LaunchServices has an undocumented API to get all applications. It probably checks */Network/Applications*, as well.

see http://developer.apple.com/technotes/tn/tn2029.html

----

**A note on all of these solutions for finding Applications directories.**

How about just asking Cocoa? It may come back with a longer list than you were expecting.

    
	NSArray *array = NSSearchPathForDirectoriesInDomains(NSAllApplicationsDirectory,  NSAllDomainsMask, TRUE);
	NSEnumerator *dirs = [array objectEnumerator];
	NSString *path;
	while (path = [dirs nextObject])
		NSLog(@"%@", path);


----

**All Running Application Names**

I am trying to create a NSTableView that contains all running application names. My problem code is this:

    return processList objectAtIndex:rowIndex] objectForKey: @"[[NSApplicationName"];

processList is an NSArray * initialized in the init routine of the dataSource object initialized by:

    
- (id)init
{
	processList = [[NSWorkspace sharedWorkspace] launchedApplications];
	return self;
}


 The pointer fails in 

    -(id)tableView:(NSTableView *)aTableView objectValueForTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex

----

try

    processList = [[[NSWorkspace sharedWorkspace] launchedApplications] **retain**];.  See MemoryManagement for some pointers.  -- Bo

----

The code above for searching for applications won't necessarily get all of them (it misses unbundled applications). The correct way to do this is certainly LaunchServices, it provides an API for testing to see if a URL is an application or not IIRC. There is a very easy (but private) interface for copying a list of all applications registered with its database -- if you'd like this to be made public then please file an enhancement request with the BugReporter. --FinlayDobbie

----

In my program, I wish to list all application files (except the Utilities folder) all inside a NSTableView.

I managed to do the applescript, which doesn't exclude the utilities folder. I can paste the line I want to run:
tell application "Finder" to return name of application files of entire contents of (path to applications folder)

When I run this it performs well. It gives me a big array of applications. The question is: How do i take this information into my objective-c application?

----

I don't think AppleScript is the best way to attack this.  Perhaps you could investigate LaunchServices.

see also http://developer.apple.com/technotes/tn/tn2029.html

some excerpts from the page:
    
NSArray *urls;
_LSCopyAllApplicationURLs(&urls);


_LSCopyAllApplicationiURLs is unsupported, you have to supply the prototype yourself.
The following code worked OK on my 10.3.9:

    
#import <Foundation/Foundation.h>
extern void _LSCopyAllApplicationURLs(NSArray**);

int main(){
        id pool=[[NSAutoreleasePool alloc] init];
        NSArray *urls;
        _LSCopyAllApplicationURLs(&urls);
        int i;
        for(i=0;i<[urls count];i++){
                NSLog(@"%@",[urls objectAtIndex:i]);
        }
        [pool release];
        return 0;
}


Save, say, to foo.m and compile with "gcc -o foo foo.m -framework Foundation -framework Carbon" and run ./foo !

If what you're after is a listing of the /Applications folder, try using NSFileManager to list all files in /Applications. You can then filter them however you want to: only show the ones with a ".app" extension, etc. LaunchServices and the Finder have more sophisticated definitions of what "all applications" means (it's not just simply a list of the files in /Applications).

----

This seems to work, but takes ages  to load! Can I make it more effective somehow?
    
	ApplicationList = [[NSMutableArray alloc] init];
	UtilityList = [[NSMutableArray alloc] init];
	NSString *file;
	NSString *appsDir = @"/Applications";
	NSDirectoryEnumerator *dirEnum = [[NSFileManager defaultManager] enumeratorAtPath: appsDir];
	while (file = [dirEnum nextObject]) {
		if (file pathExtension] isEqualToString: @"app"]) {
			if(![[file pathComponents] containsObject:@"Utilities"]) {
				[[[ApplicationList addObject: file];
			} else  {
				[UtilityList addObject: file];
			}
		}
	}


It sometimes displays the contents in the app files! like Program.app/Contents ...

----

Your code will display the package contents of any app in /Applications/Utilities/. Use     - (BOOL)isFilePackageAtPath:(NSString *)fullPath from NSWorkspace to check for packages, not an .app extension.

Your code is flawed, though. Try something like

    
		if(!file pathComponents] containsObject:@"Utilities"]){
			if ([[[[NSWorkspace sharedWorkspace] isFilePackageAtPath:file]) {
				[ApplicationList addObject: file];
                            }
			} else  {
			if ([[NSWorkspace sharedWorkspace] isFilePackageAtPath:file]) {
				[UtilityList addObject: file];
                            }
			}
		}


This will only find apps which are packages anyway. Some Carbon apps aren't, and RealBasic apps aren't. Using LaunchServices to find all apps, then checking if the returned apps are in /Applications would be a better way.

