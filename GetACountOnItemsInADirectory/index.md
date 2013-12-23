---
layout: page
title: GetACountOnItemsInADirectory
---

Is there a faster way to get the count of items in a directory than     [[[NSFileManager defaultManager] directoryContentsAtPath:aPath] count]; ? Because I'm not interested in the items but only in the amount of items in that folder.

----
FSGetCatalogInfo should be much faster at obtaining this information, at least on HFS+ drives.
----
OK, thanks. any hint which Info I need to query? kFSCatInfoFinderInfo? or kFSCatInfoValence - says it's for folders only... or kFSCatinfoFinderXInfo?
----
kFSCatInfoValence is what you want. -W
----
Great, thanks. It also counts invisible files... any way to disable that?
----
Nope. The question of whether a file is invisible or not is quite complex. You will have to walk the directory contents and determine each file's visibility individually.
----
Darn. well that defeats the whole purpose of the "avoiding nsfilemanager" thing. Is there something faster than directoryContentsAtPath?

----
FSGetCatalogInfoBulk. However, the cost needed to determine visibility of each file is likely to outweigh the cost of iterating the directory.

----
NSFileManager is used throughout the system and is basically just a wrapper for the appropriate POSIX and Carbon calls. Are you sure that NSFileManager is the cause of your bottleneck in this case?
----
Apparently not. I just tested it, a count of nsfilemanager's directorycontentsatpath is much faster than FSGetCatalogInfo (talking about milliseconds)... bottleneck sure is somewhere else.

----

The fastest way to count files on OS X is with fts_open(). This is a BSD function and it allocates memory sparingly. You can specify if you want each file's stat or not with the flag FTS_NOSTAT. Just "man fts_open" for more info. --zootbobbalu

    
#include <sys/types.h>
#include <sys/stat.h>
#include <fts.h>
#include <CoreFoundation/CoreFoundation.h>
#include "fcntl.h"
#include "unistd.h"

int main(int argc, char *argv[]) {


	FTS *tree = NULL;
	char *dirs[2] = {nil, nil};
	FTSENT *entry = NULL;
	unsigned dircount = 0, file_count = 0;
	CFAbsoluteTime last = 0.0f, start = 0.0f;

	if (argc < 2) {
		printf("fts_open <directory path>\n");
		goto bail;
	}
 
	dirs[0] = argv[1];
	
	if (!(tree = fts_open(&dirs[0], FTS_PHYSICAL | FTS_NOSTAT, NULL))) {
		printf("unable to open directory at path: %s\n", argv[1]);
		goto bail;
	}
	
	start = CFAbsoluteTimeGetCurrent();
	while (entry = fts_read(tree)) {
	
		switch (entry->fts_info) {
			case FTS_D:;
				dircount++;
				CFAbsoluteTime now = CFAbsoluteTimeGetCurrent();
				if (now - last > 0.2f) {
					printf("%05i|%6.1f sec|%s\n", dircount, now - start, entry->fts_path);
					last = now;
				}

				break;
			case FTS_F:;
				 file_count++;
				break;
			default: break;
		}
	}
	printf("dircount: %i file_count: %i time: %.1f sec\n", dircount, file_count, CFAbsoluteTimeGetCurrent() - start);

bail:;

	if (tree) 
		fts_close(tree);

	return 0;
}



