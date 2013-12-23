---
layout: page
title: SavingFilePackageWithManySmallIndependentFiles
---

This is just the code for a scenario described in NSFileWrapper. Hopefully, this is a scenario that other might find useful! Now, I want some Comments (see  bottom of the page).

It only saves the 'Main' part of a document. The rest of the document consists of 'Jobs' files that are being manipulated elsewhere and saved on the fly. The code to save those job files is not here, but this is not the point of that page, and is relatively trivial.

CharlesParnot

    
    - (NSData *)dataRepresentationOfType:(NSString *)aType
    {
        NSMutableData *data=[NSMutableData data];
        NSKeyedArchiver *archiver=
                [[NSKeyedArchiver alloc] initForWritingWithMutableData:data];
        [archiver encodeObject:mainObject forKey:@"MainObject"];
        [archiver release];
        return data;
    }

    - (BOOL)writeWithBackupToFile:(NSString *)newPath ofType:(NSString *)docType saveOperation:(NSSaveOperationType)saveType
    {
        NSString *mainPath,*mainBackup;
        NSString *currentFileName, *currentJobPath,*newJobPath;
        NSFileManager *manager=[NSFileManager defaultManager];
        NSData *mainData;
        NSFileWrapper *newJobs,*newMain,*newFile;
        BOOL returnedFlag;
        NSDictionary *attributes=[self fileAttributesToWriteToFile:newPath
                                                             ofType:docType
                                                 saveOperation:saveType];
    
        //this is the data with the main information on the document
        mainData=[self dataRepresentationOfType:@"MyType"];
    
        //case of a standard save
        if (saveType==NSSaveOperation) {
            //create a backup Main file with a tilde
            mainPath=[newPath stringByAppendingPathComponent:@"Main"];
            mainBackup=[mainPath stringByAppendingString:@"~"];
            if ([manager fileExistsAtPath:mainBackup]
                && ![manager removeFileAtPath:mainBackup handler:nil])
                return NO;
            if ([manager fileExistsAtPath:mainPath]
                && ![manager movePath:mainPath toPath:mainBackup handler:nil])
                return NO;
            //now add the new main file
            returnedFlag=[mainData writeToFile:mainPath atomically:NO];
            if (returnedFlag)
                returnedFlag=[manager changeFileAttributes:attributes atPath:mainPath];
            //and remove the backup file
            if (returnedFlag) {
                [manager removeFileAtPath:mainBackup handler:nil];
                return YES;
            } else {
                [manager removeFileAtPath:mainPath handler:nil];
                [manager movePath:mainBackup toPath:mainPath handler:nil];
                return NO;
            }
        }
    
        //case of a save as or save to
        if (![docType isEqualToString:@"BiockinPackage"])
            return NO;
        currentFileName=[self fileName];
        if ([currentFileName isEqualToString:newPath])
            return NO;
        if ([manager fileExistsAtPath:newPath]
            && ![manager removeFileAtPath:newPath handler:nil])
            return NO;
        //create a file wrapper with no Jobs directory
        newMain=[[NSFileWrapper alloc] initRegularFileWithContents:mainData];
        newFile=[[NSFileWrapper alloc] initDirectoryWithFileWrappers:
            [NSDictionary dictionaryWithObjectsAndKeys:newMain,@"Main",nil]];
        //save it at the new location
        returnedFlag=[newFile writeToFile:newPath atomically:YES updateFilenames:NO];
        [newFile release];
        [newMain release];
        //now copy the jobs from the original file
        if (returnedFlag) {
            currentJobPath=[currentFileName stringByAppendingPathComponent:@"Jobs"];
            newJobPath=[newPath stringByAppendingPathComponent:@"Jobs"];
            returnedFlag=returnedFlag &&
                         [manager copyPath:currentJobPath toPath:newJobPath handler:nil];
        }
        //set the attributes (do it last in case this causes problems?)
        returnedFlag=returnedFlag &&
               [manager changeFileAttributes:attributes atPath:newPath];

        return returnedFlag;
    }


    - (BOOL)loadDataRepresentation:(NSData *)data ofType:(NSString *)aType
    {
        NSKeyedUnarchiver *unarchiver=[[NSKeyedUnarchiver alloc] initForReadingWithData:data];
        [self setMainObject:     [unarchiver decodeObjectForKey:@"MainObject"]];
        [unarchiver release];
        return YES;
    }


    - (BOOL)loadFileWrapperRepresentation:(NSFileWrapper *)wrapper ofType:(NSString *)docType
    {   
        //case where file is a package, then Main is the file that we want inside
        NSFileWrapper *mainFile;
        if ([wrapper isDirectory]) {
            mainFile=wrapper fileWrappers] objectForKey:@"Main"];
            //maybe only a backup is available
            //(if there was a problem during saving)
            if (mainFile==nil)
                mainFile=[[wrapper fileWrappers] objectForKey:@"Main~"];
        }
    
        //problem?
        if (mainFile==nil || ![mainFile isRegularFile])
            return NO;
    
        //only load the contents of the 'Main' file
        return [self loadDataRepresentation:[mainFile regularFileContents] ofType:@"[[MyType"];
    }



----
**Comments**

What about an iMovie-like program? Is this the way to grab QuickTime data and add movie files to the package? Or should I use NSData to encode the movie file before adding it into the package. Users should be able to copy the file and go anywhere, with the video files inside.

"See code on NSFileWrapper"

