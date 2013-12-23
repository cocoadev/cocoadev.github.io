---
layout: page
title: QuartzComposerFileInputParameters
---



A QTMovie initalized with a QuartzComposerFile can be customized by modifying the input parameters of the file. Quartz Composer allows you to publish input ports. At the root patch level, select any input port on any patch. If you control click on the port, the popup menu will give you the option to publish the input. After you select the input port to publish, you will be asked to name this input. Now save the file and open this file as an NSMutableDictionary. 

The code below shows you how to change an input parameter named "Movie Path" to another source file. The input parameter "Movie Path" is the default name given to an "Image With Movie" patch's input port. Input parameters are keyed with strings of the input names modified slightly (spaces are replaces with underscores). So an input name of "Movie Path" is keyed as "Movie_Path". 

    
- (void)openQuartzComposerFileAtPath:(NSString *)path source:(NSString *)sourcePath {
    if (path pathExtension] isEqualToString:@"qtz"]) {
		[[NSMutableDictionary *plist = 
			[NSPropertyListSerialization propertyListFromData:[NSData dataWithContentsOfFile:path]
											 mutabilityOption:NSPropertyListMutableContainersAndLeaves
													   format:(void *)NULL 
											 errorDescription:(void *)NULL];
		if ([plist isKindOfClass:[NSMutableDictionary class]]) {
			NSMutableDictionary *inputParams = [plist valueForKey:@"inputParameters"];
			if ([inputParams isKindOfClass:[NSMutableDictionary class]]) {
				[inputParams setValue:sourcePath forKey:@"Movie_Path"];
				[plist writeToFile:@"/tmp/modified.qtz" atomically:YES];
				NSWorkspace *sws = [NSWorkspace sharedWorkspace];
				[sws openFile:@"/tmp/modified.qtz" withApplication:@"QuickTime Player"];
			}
		}
    }

}

