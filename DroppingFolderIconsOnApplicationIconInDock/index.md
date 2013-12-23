---
layout: page
title: DroppingFolderIconsOnApplicationIconInDock
---





To configure your applications so a user is allowed to drop a folder icon from the finder onto the application icon (i.e. to have a document based application respond to this action by having an NSDocument object handle the contents of the folder) do the following:


*Create a Cocoa document based application in Xcode named **A<nowiki/>ppForDroppedFolders** (File -> New Project -> Cocoa Document-based Application) 
*In the Group&Files outline view open **Targets** and select **A<nowiki/>ppForDroppedFolders**
*With **A<nowiki/>ppForDroppedFolders** highlighted blue select the **Get Info** menu item (File -> Project -> Get Info)
*A project info window should open. This window will have a tab view. Select the tab named **Properties**.
*In the "Document Types:" section add a new type (press the "+" button in the bottom left corner) and edit the **OS Type** field so that it reads "fold" without quotes. 


Now open "MyDocument.m" in an editor and add the following method to this file:

    
- (BOOL)loadFileWrapperRepresentation:(NSFileWrapper *)wrapper ofType:(NSString *)type {
    BOOL isValidWrapper = NO;
    if ([wrapper isDirectory]) {
        NSDictionary *fileWrappers = [wrapper fileWrappers];
        NSArray *keys = [fileWrappers allKeys];
        NSEnumerator *keyEnum = [keys objectEnumerator]; id key;
        while (key = [keyEnum nextObject]) {
            id entry = [fileWrappers objectForKey:key];
            if ([entry isDirectory]) continue;
            else if ([entry isSymbolicLink]) continue;
            NSString *filename = [entry filename];
            NSData *fileData = [entry regularFileContents];
            NSLog(@"filename: %@ dataLength: %i", filename, [fileData length]);
            isValidWrapper = YES;
        }
    }
    return isValidWrapper;
}



This method shows one way you can use this type of drop action. An NSDocument instance will be sent this message when a folder icon is dragged from a finder window onto the application icon in the dock. This particular implementation only handles files contained within the directory wrapper, but hopefully you get the general idea how to handle other situations. 

--zootbobbalu

----

I believe using NSDocument for this isn't the best idea out there, because NSDocument isn't really meant to open generic documents on behalf of its application -- you can do this without having to use NSDocument, by specifying a "fold" entry in your Info.plist's document types section that has no associated class, and then use the -applicationOpenFile: or-whatever-it's-called NSApplication delegate method. -- l0ne aka EmanueleVulcano

----

To expand on the above, you'd put something like this in your Info.plist:

    
	<key>CFBundleDocumentTypes</key>
	<array>
		<dict>
			<key>CFBundleTypeOSTypes</key>
			<array>
				<string>fold</string>
			</array>
		</dict>
	</array>


and do something in the - (BOOL)application:(NSApplication *)theApplication openFile:(NSString *)filename delegate method. -- smcbride

