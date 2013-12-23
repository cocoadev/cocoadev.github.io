---
layout: page
title: CoreDataNSDocumentPackagesSubclass
---

Moved from FolderBasedDocuments... A (possible) method to have CoreData-based documents that are folders

I followed the above instructions to create an app that would open packaged documents, and then modified the MyDocument class so that the document has a managedObjectContext just like an NSPersistentDocument would. Let me know what you think. A bug that I've found is that it doesn't show the black dot in the close button when there are unsaved changes. Other than that, it seems to work for me... Here's the code:

    

//
//  MyDocument.h
//  CDDocTest
//
//  Last updated by Jason Terhorst on 2/7/07.
//  Copyright Jason Terhorst 2007 . All rights reserved.
//


#import <Cocoa/Cocoa.h>

@interface MyDocument : NSDocument
{
	NSPersistentStoreCoordinator *persistentStoreCoordinator;
    NSManagedObjectModel *managedObjectModel;
    NSManagedObjectContext *managedObjectContext;
	
	NSString * resourceFileName;
}

- (NSPersistentStoreCoordinator *)persistentStoreCoordinator;
- (NSManagedObjectModel *)managedObjectModel;
- (NSManagedObjectContext *)managedObjectContext;

@end



//  MyDocument.m
//  CDDocTest
//
//  Last updated by Jason Terhorst on 2/7/07.
//  Copyright Jason Terhorst 2007 . All rights reserved.
//

#import "MyDocument.h"

@implementation MyDocument

- (id)init
{
    self = [super init];
    if (self) {
		
        // Add your subclass-specific initialization here.
        // If an error occurs here, send a [self release] message and return nil.
		
		resourceFileName = [[self displayName] copy] stringByDeletingPathExtension] retain];
		
    }
    return self;
}

- ([[NSString *)windowNibName
{
    // Override returning the nib file name of the document
    // If you need to use a subclass of NSWindowController or if your document supports multiple NSWindowControllers, you should remove this method and override -makeWindowControllers instead.
    return @"MyDocument";
}

- (void)windowControllerDidLoadNib:(NSWindowController *) aController
{
    [super windowControllerDidLoadNib:aController];
    // Add any code here that needs to be executed once the windowController has loaded the document's window.
}

- (NSManagedObjectModel *)managedObjectModel {
	
    if (managedObjectModel != nil) {
        return managedObjectModel;
    }
	
    NSMutableSet *allBundles = [[NSMutableSet alloc] init];
    [allBundles addObject: [NSBundle mainBundle]];
    [allBundles addObjectsFromArray: [NSBundle allFrameworks]];
    
    managedObjectModel = [[NSManagedObjectModel mergedModelFromBundles: [allBundles allObjects]] retain];
    [allBundles release];
    
    return managedObjectModel;
}

- (NSPersistentStoreCoordinator *) persistentStoreCoordinator {
	
    if (persistentStoreCoordinator != nil) {
        return persistentStoreCoordinator;
    }
	
    
    persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel: [self managedObjectModel]];
	
    return persistentStoreCoordinator;
}

- (NSManagedObjectContext *) managedObjectContext {
	
    if (managedObjectContext != nil) {
		return managedObjectContext;
    }
	
    NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
    if (coordinator != nil) {
        managedObjectContext = [[NSManagedObjectContext alloc] init];
        [managedObjectContext setPersistentStoreCoordinator: coordinator];
    }
    
    return managedObjectContext;
}

- (NSUndoManager *)undoManager {
    return self managedObjectContext] undoManager];
}

- (BOOL)isDocumentEdited
{
	if ([[self managedObjectContext] hasChanges])
		return YES;
	if ([[[self managedObjectContext] deletedObjects] count] > 0)
		return YES;
	if ([[[self managedObjectContext] insertedObjects] count] > 0)
		return YES;
	if ([[[self managedObjectContext] updatedObjects] count] > 0)
		return YES;
	
	return NO;
}

- ([[NSString *)applicationSupportFolder {
	
    NSArray *paths = NSSearchPathForDirectoriesInDomains(NSApplicationSupportDirectory, NSUserDomainMask, YES);
    NSString *basePath = ([paths count] > 0) ? [paths objectAtIndex:0] : NSTemporaryDirectory();
    return [basePath stringByAppendingPathComponent:@"CDDelegateTest"];
}



- (BOOL)writeToURL:(NSURL *)absoluteURL ofType:(NSString *)typeName error:(NSError **)outError
{
	
	// we use this to make sure to call the method to create a directory wrapper, then we save the store...
	BOOL success = [super writeToURL:absoluteURL ofType:typeName error:outError];
	
	if ([self fileURL] == nil)
		[self setFileURL:absoluteURL];
	
	if (success) {
		
		NSFileManager *fileManager;
		NSString *applicationSupportFolder = nil;
		NSURL *url, *finalurl;
		NSError *error;
		
		fileManager = [NSFileManager defaultManager];
		applicationSupportFolder = [self applicationSupportFolder];
		if ( ![fileManager fileExistsAtPath:applicationSupportFolder isDirectory:NULL] ) {
			[fileManager createDirectoryAtPath:applicationSupportFolder attributes:nil];
		}
		
		url = [NSURL fileURLWithPath: [applicationSupportFolder stringByAppendingPathComponent: resourceFileName ]];
		finalurl = [NSURL fileURLWithPath: [self fileURL] path] stringByAppendingPathComponent: @"[[CDDocTest.xml"]];
		
		if ([[NSFileManager defaultManager] fileExistsAtPath:[url path]]) {
			if ([[NSFileManager defaultManager] removeFileAtPath:[url path] handler:nil]) {
				NSLog(@"existing file - removed successfully");
			}
		}
		
		NSLog(@"file url: %@", [url path]);
		
		if (persistentStoreCoordinator persistentStores] count] == 0) {
			
			if (![persistentStoreCoordinator addPersistentStoreWithType:[[NSXMLStoreType configuration:nil URL:url options:nil error:&error]){
				[[NSApplication sharedApplication] presentError:error];
			} else {
				NSError * error = nil;
				if (!self managedObjectContext] save:&error]) {
					[[[[NSApplication sharedApplication] presentError:error];
				}
			}			
		} else {
			NSError * error = nil;
			if (!self managedObjectContext] save:&error]) {
				[[[[NSApplication sharedApplication] presentError:error];
			}			
		}	
		
		if ([[NSFileManager defaultManager] fileExistsAtPath:[url path]]) {
			NSLog(@"file exists");
			if ([[NSFileManager defaultManager] copyPath:[url path] toPath:[finalurl path] handler:nil]) {
				NSLog(@"file was successfully copied");
			}
		}
		
		
		return YES;
	}
	
	return NO;
}

- (NSFileWrapper *)fileWrapperOfType:(NSString *)typeName error:(NSError **)outError
{
	NSFileWrapper * dirWrapper = [[NSFileWrapper alloc] initDirectoryWithFileWrappers:nil];
	
	NSLog(@"file url: %@", self fileURL] path]);
	
	return dirWrapper;
	
}

- (BOOL)readFromFileWrapper:([[NSFileWrapper *)fileWrapper ofType:(NSString *)typeName error:(NSError **)outError
{
	NSURL *url, *originalurl;
	NSError *error;
	
	resourceFileName = [[self displayName] copy] stringByDeletingPathExtension] retain];
	
	originalurl = [NSURL fileURLWithPath: [[[self fileURL] path] stringByAppendingPathComponent: @"[[CDDocTest.xml"]];
	url = [NSURL fileURLWithPath: self applicationSupportFolder] stringByAppendingPathComponent: resourceFileName ;
	
	NSLog(@"file url: %@", [url path]);
	
	if ([[NSFileManager defaultManager] fileExistsAtPath:[url path]]) {
		if (![[NSFileManager defaultManager] removeFileAtPath:[url path] handler:nil]) {
			NSLog(@"couldn't delete the file that's already there");
		}
	}
	
	if ([[NSFileManager defaultManager] fileExistsAtPath:[originalurl path]]) {
		NSLog(@"file exists");
		if ( ![[NSFileManager defaultManager] fileExistsAtPath:[self applicationSupportFolder] isDirectory:NULL] ) {
			[[NSFileManager defaultManager] createDirectoryAtPath:[self applicationSupportFolder] attributes:nil];
		}
		if ([[NSFileManager defaultManager] copyPath:[originalurl path] toPath:[url path] handler:nil]) {
			NSLog(@"file was successfully copied");
		}
	}
	
	persistentStoreCoordinator = [self persistentStoreCoordinator];
	if (persistentStoreCoordinator persistentStores] count] > 0)
		[[NSBeep();
	
	if (![persistentStoreCoordinator addPersistentStoreWithType:NSXMLStoreType configuration:nil URL:url options:nil error:&error]){
		[[NSApplication sharedApplication] presentError:error];
		NSLog(@"failed to load");
	} else {
		managedObjectContext = [self managedObjectContext];
		return YES;
	}
	
	return NO;
}

- (BOOL)revertToContentsOfURL:(NSURL *)absoluteURL ofType:(NSString *)typeName error:(NSError **)outError
{
	NSLog(@"reverting file");
	
	NSURL *url, *originalurl;
	NSError *error;
	
	persistentStoreCoordinator = [self persistentStoreCoordinator];
	NSArray * stores = [persistentStoreCoordinator persistentStores];
	if (![persistentStoreCoordinator removePersistentStore:[stores objectAtIndex:0] error:&error]) {
		[[NSApplication sharedApplication] presentError:error];
		NSLog(@"could not remove the current store");
		return NO;
	}	
	
	resourceFileName = [[self displayName] copy] stringByDeletingPathExtension] retain];
	
	originalurl = [NSURL fileURLWithPath: [[absoluteURL path] stringByAppendingPathComponent: @"[[CDDocTest.xml"]];
	url = [NSURL fileURLWithPath: self applicationSupportFolder] stringByAppendingPathComponent: resourceFileName ;
	
	if ([[NSFileManager defaultManager] fileExistsAtPath:[url path]]) {
		if (![[NSFileManager defaultManager] removeFileAtPath:[url path] handler:nil]) {
			NSLog(@"couldn't delete the file that's already there");
		}
	}
	
	NSLog(@"file url: %@", [url path]);
	
	if ([[NSFileManager defaultManager] fileExistsAtPath:[originalurl path]]) {
		NSLog(@"file exists");
		if ( ![[NSFileManager defaultManager] fileExistsAtPath:[self applicationSupportFolder] isDirectory:NULL] ) {
			[[NSFileManager defaultManager] createDirectoryAtPath:[self applicationSupportFolder] attributes:nil];
		}
		if ([[NSFileManager defaultManager] copyPath:[originalurl path] toPath:[url path] handler:nil]) {
			NSLog(@"file was successfully copied");
		}
	}
	
	error = nil;
	
	if (![persistentStoreCoordinator addPersistentStoreWithType:NSXMLStoreType configuration:nil URL:url options:nil error:&error]){
		[[NSApplication sharedApplication] presentError:error];
		NSLog(@"failed to load");
		return NO;
	} else {
		managedObjectContext = [self managedObjectContext];
		return YES;
	}
	
	return NO;
}

- (void) dealloc {
	
    [managedObjectContext release], managedObjectContext = nil;
    [persistentStoreCoordinator release], persistentStoreCoordinator = nil;
    [managedObjectModel release], managedObjectModel = nil;
    [super dealloc];
}

@end



Could you perhaps post the complete project to make it easy to see what this does? I am interested in doing something like this in my application.

