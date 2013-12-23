---
layout: page
title: NSFileWrapper
---

NSFileWrapper watches files and directories for changes outside of the application's control. For instance, you can set up a file wrapper on a file and if the user changes the file outside of your application, you can use the file wrapper to continue to access the file independent of the user's changes.

----

Does anyone have any experience in using file wrappers as the document type for an application? Are there any tutorials available?

Specifically, the document receives two messages regarding the file wrapper: fileWrapperRepresentationOfType: and loadFileWrapperRepresentation:ofType:. So during the course of the user editing the document, let's say the user does something which causes a file to be added to the wrapper directory representing the user's document. What wrapper do I use to add this file? Should I retain the one from the loadFileWrapperRepresentation:ofType: message?

--ChrisMeyer

----

I removed some stuff I put here, because I want to give a better answer to the question, after implementing my document saving. My feeling is that the answer depends on the size of the file. I think an NSFileWrapper instance size is directly dependent on the size of the associated file, as it is really a copy of a file in memory  (either loaded from disk or being created from scratch with some NSData). Please if somebody knows that this is not true, say it here and delete the rest...

If the document size is typically quite small, you could retain the NSFileWrapper during     loadFileWrapperRepresentation:ofType:, and use it to add a new file when     fileWrapperRepresentationOfType: is called. This way, you can update the NSFileWrapper elements that need updating (i.e. files that need updating) and return the NSFileWrapper instance. If the file is big however, this means that the whole file is always loaded in memory, and that you rewrite the whole file every time you save.

Now, here is the scenario that I have been facing, and the answer that I found was the most appropriate. My document consists of a Main file, relatively small (typically 50-500 kb), and then 'Jobs' files that can have any size and that can be numerous. These job files are meant to be manipulated independently: the user create new jobs or delete them, or read them, and this is independent from the 'Main' document, which contains some common data for all the 'Jobs'. So when the user saves the document, only the 'Main' part needs to be really saved. The 'Jobs' are manipulated elsewhere. I could apply the above scheme, and save the whole document every time I need (adding jobs, deleting jobs, updating the 'Main'). The problem is the file will usually be many megabytes, because of all these 'Jobs'. And even if it is only 1 MB, saving the whole thing when only one Job (~10-100 kb) was changed is a big waste.

Here is a copy of the code that I wrote for NSDocument : SavingFilePackageWithManySmallIndependentFiles

The bottom line is that loading the document uses     loadFileWrapperRepresentation:ofType:, while writing the files uses     writeWithBackupToFile:ofType:saveOperation:. The loading will thus load the whole document in memory with the NSFileWrapper, but just once and then the NSFileWrapper is released (one could make the loading better using the     readFromFile:ofType: method). The saving needs more code to save memory and time usage. It only saves the 'Main' part if saving is done in place (with some intermediate backup  in case things go wrong), and uses NSFileManager to copy the Jobs to the new file in case of a Save As/To operation.

CharlesParnot

----

Keep in mind that performance will do nothing but suck if the files you are placing into the wrapper are large. NSFileWrapper is slow, to say the least. It is recommended that if you are doing something like iMovie - dealing with huge video files, for instance - you should try NSFileManager. For example:

    

    - (BOOL)writeToFile:(NSString *)fileName ofType:(NSString *)docType
    {
        if ([super writeToFile:fileName ofType:docType])
        {
            NSArray * clipFiles = [slides valueForKey:@"clipFile"];
        
            int x;
            int count = [slides count];
        
            [saveProgressPanel orderFront:self];
            [saveProgressBar startAnimation:self];
        
            for (x = 0; x < count; x++)
            {
                NSString * iteratedFile;
                NSURL * fileURL = [NSURL URLWithString:[clipFiles objectAtIndex:x]];
            
                iteratedFile = [fileURL path];
            
                NSLog(@"saving...");
            
                if (clipFiles objectAtIndex:x] length] > 0)
                {
                    if ([[[[NSFileManager defaultManager] fileExistsAtPath:iteratedFile] == YES)
                    {
                        if ([[NSFileManager defaultManager] fileExistsAtPath:[fileName stringByAppendingString:[@"/" stringByAppendingString:[iteratedFile lastPathComponent]]]])
                        {
                            NSLog(@"it's already saved in the file.");
                        }
                        else
                        {
                            if ([[NSFileManager defaultManager] fileExistsAtPath:[[NSHomeDirectory() stringByAppendingPathComponent:@"Library/AppName"] stringByAppendingPathComponent:clipFiles objectAtIndex:x] lastPathComponent] == YES)
                                [[NSFileManager defaultManager] copyPath:[[NSHomeDirectory() stringByAppendingPathComponent:@"Library/AppName"] stringByAppendingPathComponent:clipFiles objectAtIndex:x] lastPathComponent] toPath:[[fileName stringByAppendingString:@"/"] stringByAppendingString:[iteratedFile lastPathComponent handler:nil]];
                            else
                                [[NSFileManager defaultManager] copyPath:iteratedFile toPath:fileName stringByAppendingString:@"/"] stringByAppendingString:[iteratedFile lastPathComponent handler:nil];
                        }
                    }
                    else
                        NSLog(@"this file cannot be found.");
                }
                else
                    NSLog(@"no file");
            }
            [saveProgressBar stopAnimation:self];
            [saveProgressPanel orderOut:self];
            return YES;
        }
        return NO;
    }



Of course, you must still use one of the fileWrapper or fileOfType methods along with it (writeToFile doesn't replace those, it just gives you more control)...

    

    - (NSFileWrapper *)fileWrapperRepresentationOfType:(NSString *)aType
    {
        if (self fileName] length] > 0)
        {
            [[NSMutableArray * savedPaths = [NSBundle pathsForResourcesOfType:nil inDirectory:[self fileName]];
            int count = [savedPaths count];
            int x;
            for (x = 0; x < count; x++)
            {
                if (savedPaths objectAtIndex:x] pathExtension] == @"data")
                    [[NSLog(@"ignoring data file");
                else
                {
                    if ([[NSFileManager defaultManager] fileExistsAtPath:[NSHomeDirectory() stringByAppendingPathComponent:@"Library/AppName"] isDirectory:YES] == YES)
                    {
                        if ([[NSFileManager defaultManager] movePath:[savedPaths objectAtIndex:x] toPath:[[NSHomeDirectory() stringByAppendingPathComponent:@"Library/AppName"] stringByAppendingPathComponent:savedPaths objectAtIndex:x] lastPathComponent] handler:nil == YES)
                            NSLog(@"success!!");
                        else
                            NSLog(@"error copying file");
                    }
                    else if ([[NSFileManager defaultManager] createDirectoryAtPath:[NSHomeDirectory() stringByAppendingPathComponent:@"Library/AppName"] attributes:nil] == YES)
                    {
                        if ([[NSFileManager defaultManager] movePath:[savedPaths objectAtIndex:x] toPath:[[NSHomeDirectory() stringByAppendingPathComponent:@"Library/AppName"] stringByAppendingPathComponent:savedPaths objectAtIndex:x] lastPathComponent] handler:nil == YES)
                            NSLog(@"success!!");
                        else
                            NSLog(@"error copying file");
                    }
                }
            }
        }
    
        NSMutableDictionary * fileWrappers;
        fileWrappers = [NSMutableDictionary dictionaryWithCapacity:1];
        [fileWrappers setObject:[[NSFileWrapper alloc] initRegularFileWithContents:[NSKeyedArchiver archivedDataWithRootObject:slides]] forKey:@"slides.idata"];
        NSFileWrapper * wrapper = [[NSFileWrapper alloc] initDirectoryWithFileWrappers:fileWrappers];
    
        return wrapper;
    }

    - (BOOL)loadFileWrapperRepresentation:(NSFileWrapper *)wrapper ofType:(NSString *)type {
        currentFileName = [self fileName];
        NSDictionary * contents = [wrapper fileWrappers];
        NSFileWrapper * objects = [contents objectForKey:@"slides.idata"];
        NSData * objectData = [objects regularFileContents];
        NSMutableArray * newSlides;
        newSlides = [NSKeyedUnarchiver unarchiveObjectWithData:objectData];
        if (newSlides == nil) {
            return NO;
        } else {
            NSArray * slideTypes = [newSlides valueForKey:@"slideType"];
            NSArray * clipNames = [newSlides valueForKey:@"clipName"];
            NSArray * clipFiles = [newSlides valueForKey:@"clipFile"];
            NSArray * backgroundColors = [newSlides valueForKey:@"backgroundColor"];
            NSArray * slideTitles = [newSlides valueForKey:@"slideTitle"];
            NSArray * slideBodys = [newSlides valueForKey:@"slideBody"];
            NSArray * isLoops = [newSlides valueForKey:@"isLoop"];
            NSArray * loopTargets = [newSlides valueForKey:@"loopTarget"];
            NSArray * loopRepeats = [newSlides valueForKey:@"loopRepeat"];
        
            int x;
            int count = [newSlides count];
        
            for (x = 0; x < count; x++)
            {
                VideoSlide * slide = [[VideoSlide alloc] init];
                [slide retain];
            
                [slide setSlideType:[slideTypes objectAtIndex:x]];
                [slide setClipName:[clipNames objectAtIndex:x]];
                [slide setClipFile:[clipFiles objectAtIndex:x]];
                [slide setSlideTitle:[slideTitles objectAtIndex:x]];
                [slide setSlideBody:[slideBodys objectAtIndex:x]];
                [slide setBackgroundColor:[backgroundColors objectAtIndex:x]];
                [slide setIsLoop:[isLoops objectAtIndex:x]];
                [slide setLoopTarget:[loopTargets objectAtIndex:x]];
                [slide setLoopRepeat:[loopRepeats objectAtIndex:x]];
            
                [slides insertObject:slide atIndex:[slides count]];
                [slide release];
            }
            [table reloadData];
        
            return YES;
        }
    }



This is taken from something I'm working on, but should give you a rough idea. I was working with video files, some that were HD, and thus were huge. It wasn't performing fast enough to just tie into a wrapper, so I still made a wrapper, but didn't put anything in it. Then, just call that additional method, and allowed me to tie in whatever else I wanted. Something that you don't see in this code is that, after loading the file, those files aren't loaded into memory (like a traditional file wrapper). Rather, I pull the file's name from the string in the array, and hunt down the file's path using that filename and the saved file path. If it can't find it there, I can always search elsewhere. This seems to work for me, even with 3 gigabyte files.
Even though it's big, I've posted this here because I had difficulty finding helpful info on NSFileWrapper anywhere online; people would say "don't use it", or they would just say "it's easy, look in the docs"....

-- JasonTerhorst

