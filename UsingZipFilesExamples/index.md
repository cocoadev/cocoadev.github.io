---
layout: page
title: UsingZipFilesExamples
---



I searched the site for using zip files in Cocoa and all I came up with was using ditto. Does anyone have an example of something like this:

Select zip archive,
Read archive and display in window,
Change name of file in archive,
Rezip

Onto the next one. Basically just the reading of the zip, making a change and rezipping. 

Any help would be appreciated.

----

There's the General/FFArchive framework, which is part of the General/FFView application at http://www.feedface.com/projects/ffview.html. -- l0ne aka General/EmanueleVulcano

----

Alternatively, you could read the documentation for 'ditto'  (open terminal, type "man ditto") and create and launch a task. To get you started, here's a quick way of zipping the contents of a folder...

    
 /* Assumes sourcePath and targetPath are both
 valid, standardized paths. */
 
 // Create the zip task
 NSTask * backupTask = General/NSTask alloc] init];
 [backupTask setLaunchPath:@"/usr/bin/ditto"];
 [backupTask setArguments:
  [NSArray arrayWithObjects:@"-c", @"-k", @"-X", @"--rsrc",
   sourcePath, targetPath, nil;
 
 // Launch it and wait for execution
 [backupTask launch];
 [backupTask waitUntilExit];
 
 // Handle the task's termination status
 if ([backupTask terminationStatus] != 0)
 NSLog(@"Sorry, didn't work.");
 
 // You *did* remember to wash behind your ears ...
 // ... right?
 [backupTask release];


Standard warning: This code was typed in the Wiki's edit window. It may not be perfect, efficient, sane, safe with children, etc. Figuring out ditto and how to manipulate it from within Cocoa is up to you. Well, alright, another hint. Check out General/NSTask documentation as well. In addition to asking it for its termination status, you can capture a task's output. Used with General/NSScanner, you can parse the output and make it purdy.

----
I wanted to save my document as a file that users can open as a zip file by just changing the extention to zip.
So I made the following code using the above code. 

If you don't want users to open a file as a zip archive, I think General/NSData+General/CocoaDevUsersAdditions in General/NSDataCategory is more handy to use.

    
 /*
 
 Please change the scratch folder etc to appropriate ones.
 
 */
 
 
 -(void)usageExample
 {
   // create sample data
   NSString* text = @"test";
   NSData* data = [text dataUsingEncoding:NSUTF8StringEncoding];
   
   
   
   // Zip test 1
   
   /*
   NSData* convertedData = [self zip:data];
   
   [convertedData writeToFile:General/[NSHomeDirectory() stringByAppendingPathComponent: @"Desktop/zip result"] atomically:YES];
   
   
   */
   
   // Zip test 2
   
   NSFileWrapper* zipwrap = General/[NSFileWrapper alloc] initDirectoryWithFileWrappers:NULL ] autorelease];
   
   [zipwrap addRegularFileWithContents:data   preferredFilename:@"data1" ];
   [zipwrap addRegularFileWithContents:data   preferredFilename:@"data2" ];
   
   NSData* convertedData = [self zip:zipwrap];
   
   [convertedData writeToFile:[[[NSHomeDirectory() stringByAppendingPathComponent: @"Desktop/zip result"] atomically:YES];
   
   
   
   
   // Unzip
   
   NSFileWrapper* wrapper = [self unzip:convertedData];
   
   if( [wrapper isRegularFile] )
   {
     NSLog(@"this is a regular file wrapper");
     
     
   }
   else
   {
     NSLog(@"this is a directory wrapper");
     
     
     NSDictionary* regularFileWrappers = [wrapper fileWrappers];
     
     NSLog(@"contains %@", [regularFileWrappers allKeys] );
  	
   }
   
   [wrapper  writeToFile:General/[NSHomeDirectory() stringByAppendingPathComponent: @"Desktop/unzip result"] atomically:YES updateFilenames:YES];
   
 }
 
 
 
 -(NSData*)zip:(id)raw //raw must be NSData or NSFileWrapper
 {
   NSString* scratchFolder =General/[NSHomeDirectory() stringByAppendingPathComponent: @"Desktop/"];
   
   
   NSString* sourceFilename = @"data";
   NSString* targetFilename = @"zipped data";
   
   NSString* sourcePath =[scratchFolder stringByAppendingPathComponent: sourceFilename];
   NSString* targetPath =[scratchFolder stringByAppendingPathComponent: targetFilename];
   
   
   BOOL flag = NO;
   
   
   if( [raw isKindOfClass:[NSData class]] )
     flag = [raw writeToFile:sourcePath atomically:YES];
  	
  	
  	else 	if( [raw isKindOfClass:[NSFileWrapper class]] )
       flag = [raw writeToFile:sourcePath atomically:YES updateFilenames:YES];
       
       
       if( flag == NO )
       {
  		NSLog(@"Fail to write.");
  		return NULL;
       }
   
   /* Assumes sourcePath and targetPath are both
   valid, standardized paths. */
   
   
   
   //----------------
   // Create the zip task
   NSTask * backupTask = General/NSTask alloc] init];
   [backupTask setLaunchPath:@"/usr/bin/ditto"];
   [backupTask setArguments:
    [NSArray arrayWithObjects:@"-c", @"-k", @"-X", @"--rsrc",
     sourcePath, targetPath, nil;
   
   // Launch it and wait for execution
   [backupTask launch];
   [backupTask waitUntilExit];
   
   // Handle the task's termination status
   if ([backupTask terminationStatus] != 0)
   {
     NSLog(@"Sorry, didn't work.");
     return NULL;
   }
   
   // You *did* remember to wash behind your ears ...
   // ... right?
   [backupTask release];
   
   
   NSData* convertedData = General/[NSData alloc] initWithContentsOfFile:targetPath] autorelease];
   
   //delete scratch
   
   [[NSWorkspace sharedWorkspace] performFileOperation:NSWorkspaceDestroyOperation
                                                    source:scratchFolder
                                               destination:@""
                                                     files:[NSArray arrayWithObjects:sourceFilename, targetFilename,NULL]
                                                       tag:NULL];
   
   
   return convertedData;
 }
 
 
 -(NSFileWrapper*)unzip:(NSData*)zipData
 {
   NSString* scratchFolder =[[[NSHomeDirectory()
                                  stringByAppendingPathComponent: @"Desktop/"];
   
   NSString* sourceFilename = @"zipped data";
   NSString* targetFilename = @"unzipped folder";
   
   NSString* sourcePath =[scratchFolder stringByAppendingPathComponent: sourceFilename];
   NSString* targetPath =[scratchFolder stringByAppendingPathComponent: targetFilename];
   
   
   
   BOOL flag = [zipData writeToFile:sourcePath atomically:YES];
   
   if( flag == NO )
   {
     NSLog(@"error");
     return NULL;
   }
   
   
   //Unzip
   
   //-------------------
   NSTask *cmnd=General/NSTask alloc] init];
   [cmnd setLaunchPath:@"/usr/bin/ditto"];
   [cmnd setArguments:[NSArray arrayWithObjects:
                       @"-v",@"-x",@"-k",@"--rsrc",sourcePath,targetPath,nil;
   [cmnd launch];
   [cmnd waitUntilExit];
   
   // Handle the task's termination status
   if ([cmnd terminationStatus] != 0)
   {
     NSLog(@"Sorry, didn't work.");
     return NULL;
   }
   
   // You *did* remember to wash behind your ears ...
   // ... right?
   [cmnd release];
   
   
   
   //unzip
   //
   
   NSArray* contents = General/NSFileManager defaultManager] directoryContentsAtPath:targetPath];
   
   
   NSFileWrapper* wrapper;
   
   if( [contents count] == 1 )
   {
     NSString* onepath;
     onepath = [targetPath stringByAppendingPathComponent:[contents lastObject;
     
     NSData* data = [NSData dataWithContentsOfFile:onepath];
     
     wrapper = [[[NSFileWrapper alloc] initRegularFileWithContents:data  ] autorelease];
     
     
   }
   else	if( [contents count] > 1 )
   {
     
     wrapper = [[[NSFileWrapper alloc] initDirectoryWithFileWrappers:NULL ] autorelease];
     
     unsigned hoge;
     for( hoge = 0; hoge < [contents count]; hoge++ )
     {
       NSString* onepath;
       NSString* onefilename;
       
       onefilename = [contents objectAtIndex:hoge];
       onepath = [targetPath stringByAppendingPathComponent:onefilename];
       
       NSData* data = [NSData dataWithContentsOfFile:onepath];
       
       [wrapper addRegularFileWithContents:data   preferredFilename:onefilename ];
     }
   }
   
   
   
   //delete scratch
   
   [[NSWorkspace sharedWorkspace] performFileOperation:NSWorkspaceDestroyOperation
                                                    source:scratchFolder
                                               destination:@"" 
                                                     files:[NSArray arrayWithObjects:sourceFilename, targetFilename,NULL]
                                                       tag:NULL];
   
   
   return wrapper;
 }

