---
layout: page
title: FolderBasedDocuments
---




To configure your application so you can use folders as documents do the following:


*Create a Cocoa document based application in Xcode named "AppForFolderBasedDocument" (File -> New Project -> Cocoa Document-based Application) 
*In the Group&Files outline view open **Targets** and select **AppForFolderBasedDocument**
*With **AppForFolderBasedDocument** highlighted blue select the **Get Info** menu item (File -> Project -> Get Info)
*A project info window should open. This window will have a tab view. Select the tab named **Properties**.
*In the "Document Types:" section add a new type (press the "+" button in the bottom left corner) 

*Enter the name of this kind of document (e.g. Folder Based Document) in the **Name** field
*Enter the file extension (e.g. "folderdoc") in the **Extensions** field
*Enter the name of the icon you would like the Finder to use for this type of document in the **Icon File** field (drag this icon file into this project before building the target)
*Set the class that handles the document to "MyDocument" in the **Class** field
*Set the role of that the application plays for this type of document to "Editor" in the **Role** field
*click on the button **Open Info.plist as File**
*find the entry for the new document type and modify this entry so that it includes the keyed item **LSTypeIsPackage** set to **YES**
    
			<key>CFBundleTypeExtensions</key>
			<array>
				<string>folderdoc</string>
			</array>
			<key>CFBundleTypeIconFile</key>
			<string>FolderDocIcon</string>
			<key>CFBundleTypeName</key>
			<string>Folder Based Document</string>
			<key>CFBundleTypeRole</key>
			<string>Editor</string>
			<key>LSTypeIsPackage</key>
			<string>YES</string>
			<key>NSDocumentClass</key>
			<string>MyDocument</string>


*Save Info.plist

*Create a new folder anywhere you wish and name the folder "folder_based_document.folderdoc"
*Now open "MyDocument.m" in an editor and add the following method to this file:
    
- (BOOL)loadFileWrapperRepresentation:(NSFileWrapper *)wrapper ofType:(NSString *)type {
    NSLog(@"loadFileWrapperRepresentation: %@ type: %@", [wrapper description], type);
}


*Build and Run



After your application has successfully launched one time, you have to log out and log back in for the Finder to display the icon associated with folders that have the extension "folderdoc". The folder you created named "folder_based_document.folderdoc" should now display the icon you picked when you view the folder with the Finder. Go back into XCode, open the project that contains **AppForFolderBasedDocument** and run this target so you can observe the run log. Now double click on the folder named "folder_based_document.folderdoc" in the Finder. Hopefully your running application will log the request to open this folder. 
 
--zootbobbalu

----

**Important Note:** Package-based files are **not** supported with Core Data as of 10.4.0 through 10.4.7. Though the documentation suggests you can override     configurePersistentStoreCoordinator:forURL:ofType:error: to manipulate the store's URL for use with packages, this support is broken and is a 'known issue' with Apple. There appears to be no workaround as of July 7, 2006 and the documentation has since been updated to state that this cannot be done.  *Updated July 7, 2006**

*This would probably be a good situation in which to just stick with the traditional Cocoa techniques. Once Apple irons out the CoreData stuff, we might be able to use it for packaged files.*

----

New folder-based CoreData NSDocument subclass was moved to its own page... CoreDataNSDocumentPackagesSubclass

----

*Why was this information deleted? It's true and it's helpful.*

----

Good question. I had posted my addition here, and you posted your reply, which deleted mine. What's going on?

----
My first guess would be that you have a browser which doesn't properly handle form refreshes, keeping an old version of the page text around despite the underlying form changing, but (irritatingly) updating the "olddate" hidden field, thus circumventing collision detection. This seems to happen more readily if you jump around with the back and forwards buttons in your browser while you're editing the page. Another possibility is that, while looking at the old revision of the page, you hit "revert" instead of "edit". Chances are very high that it was accidental :) -- KritTer

----

Sorry to bump this, but I wanted to get back on-topic with this page... Go ahead and take a look at the MyDocument class code above, and give me a critique if you have the time. If it works well for everyone, it would be a nice solution to the problem of not being able to use CoreData easily for Document-based file packages. (moved to its own page... CoreDataNSDocumentPackagesSubclass)

----

Apple has updated CoreData in Leopard to allow you to write your own file - the Atomic store type. Anyone know if this means we can use folder-based documents with this?

----
A better place to discuss the Core Data issue with packages is: NSPersistentDocumentAsPackage (since that's the precise and only topic of that page). No responses since I updated that last week.

