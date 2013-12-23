---
layout: page
title: MDItem
---

A Carbon object that represents the metadata of a file.

----

kMDItemTextContent attribute can't be obtained from MDItemRef.  So I wrote this code, SpotlightTextContentRetriever

It parses mdimport -L command to find mdimporter plugins, then compare the bundle identifier of each plugin and the target file's handler identifier.


This code is used in SpotInside http://www.oneriver.jp/

    
//
//  SpotlightTextContentRetriever.h
//  SpotInside
//
//  Created by Masatoshi Nishikata on 06/11/22.
//  Copyright 2006 www.oneriver.jp. All rights reserved.
//


/*
 
 Modified on 2007.11.1
 Modified on 2007.11.3
 
 */

/*
 Using codes from Apple's BasicPlugin
 ----------------------------------
 
 Description: Basic CFPlugIn sample code shell, Carbon API
 
 Copyright: 	 © Copyright 2001 Apple Computer, Inc. All rights reserved.
 
 Disclaimer:	 IMPORTANT:  This Apple software is supplied to you by Apple Computer, Inc.
 ("Apple") in consideration of your agreement to the following terms, and your
 use, installation, modification or redistribution of this Apple software
 constitutes acceptance of these terms.  If you do not agree with these terms,
 please do not use, install, modify or redistribute this Apple software.
 
 In consideration of your agreement to abide by the following terms, and subject
 to these terms, Apple grants you a personal, non-exclusive license, under Apple’s
 copyrights in this original Apple software (the "Apple Software"), to use,
 reproduce, modify and redistribute the Apple Software, with or without
 modifications, in source and/or binary forms; provided that if you redistribute
 the Apple Software in its entirety and without modifications, you must retain
 this notice and the following text and disclaimers in all such redistributions of
 the Apple Software.  Neither the name, trademarks, service marks or logos of
 Apple Computer, Inc. may be used to endorse or promote products derived from the
 Apple Software without specific prior written permission from Apple.  Except as
 expressly stated in this notice, no other rights or licenses, express or implied,
 are granted by Apple herein, including but not limited to any patent rights that
 may be infringed by your derivative works or by other works in which the Apple
 Software may be incorporated.
 
 The Apple Software is provided by Apple on an "AS IS" basis.  APPLE MAKES NO
 WARRANTIES, EXPRESS OR IMPLIED, INCLUDING WITHOUT LIMITATION THE IMPLIED
 WARRANTIES OF NON-INFRINGEMENT, MERCHANTABILITY AND FITNESS FOR A PARTICULAR
 PURPOSE, REGARDING THE APPLE SOFTWARE OR ITS USE AND OPERATION ALONE OR IN
 COMBINATION WITH YOUR PRODUCTS.
 
 IN NO EVENT SHALL APPLE BE LIABLE FOR ANY SPECIAL, INDIRECT, INCIDENTAL OR
 CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE
 GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
 ARISING IN ANY WAY OUT OF THE USE, REPRODUCTION, MODIFICATION AND/OR DISTRIBUTION
 OF THE APPLE SOFTWARE, HOWEVER CAUSED AND WHETHER UNDER THEORY OF CONTRACT, TORT
 (INCLUDING NEGLIGENCE), STRICT LIABILITY OR OTHERWISE, EVEN IF APPLE HAS BEEN
 ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
 
 */

/* Japanese Text MDImporter
 * Copyright (c) 2005-2006 KATO Kazuyoshi
 *
 * Permission is hereby granted, free of charge, to any person
 * obtaining a copy of this software and associated documentation
 * files (the "Software"), to deal in the Software without
 * restriction, including without limitation the rights to use, copy,
 * modify, merge, publish, distribute, sublicense, and/or sell copies
 * of the Software, and to permit persons to whom the Software is
 * furnished to do so, subject to the following conditions:
 *
 * The above copyright notice and this permission notice shall be
 * included in all copies or substantial portions of the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
 * EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
 * MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
 * NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS
 * BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN
 * ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
 * CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
 * SOFTWARE.
 *
 */

/*
 * guess.c - guessing character encoding 
 *
 *   Copyright (c) 2000-2003 Shiro Kawai, All rights reserved.
 * 
 *   Redistribution and use in source and binary forms, with or without
 *   modification, are permitted provided that the following conditions
 *   are met:
 * 
 *   1. Redistributions of source code must retain the above copyright
 *      notice, this list of conditions and the following disclaimer.
 *
 *   2. Redistributions in binary form must reproduce the above copyright
 *      notice, this list of conditions and the following disclaimer in the
 *      documentation and/or other materials provided with the distribution.
 *
 *   3. Neither the name of the authors nor the names of its contributors
 *      may be used to endorse or promote products derived from this
 *      software without specific prior written permission.
 *
 *   THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
 *   "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
 *   LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
 *   A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
 *   OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
 *   SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED
 *   TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR
 *   PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF
 *   LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING
 *   NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
 *   SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
 *
 *  $Id: guess.c,v 1.4 2004/10/06 09:25:36 shirok Exp $
 */




#import <Cocoa/Cocoa.h>

@interface SpotlightTextContentRetriever : NSObject {

}
+(void)initialize;
+(BOOL)loadPlugIns;
+(NSArray* )loadedPlugIns;
+(NSArray* )unloadedPlugIns;
+(NSMutableArray* )metaDataOfFileAtPath:(NSString*)targetFilePath;
+(NSString* )textContentOfFileAtPath:(NSString*)targetFilePath;
+(NSMutableDictionary*)executeMDImporterAtPath:(NSString*)mdimportPath forPath:(NSString*)path uti:(NSString*)uti;
+(int)OSVersion;
+(NSString*)readTextAtPath:(NSString*)path;

@end

//------


//
//  SpotlightTextContentRetriever.m
//  SpotInside
//
//  Created by Masatoshi Nishikata on 06/11/22.
//  Copyright 2006 www.oneriver.jp. All rights reserved.
//




#include <Carbon/Carbon.h>
#include <CoreFoundation/CoreFoundation.h>
#include <CoreFoundation/CFPlugInCOM.h>
#import "SpotlightTextContentRetriever.h"

#import "TEC.h"
#import "JapaneseString.h"


typedef struct PlugInInterfaceStruct {
    IUNKNOWN_C_GUTS;
	Boolean (*GetMetadataForFile)(void* myInstance, 
								  CFMutableDictionaryRef attributes, 
								  CFStringRef contentTypeUTI,
								  CFStringRef pathToFile);
	
} MDImporterInterfaceStruct;


static MDImporterInterfaceStruct **mdimporterInterface = nil;

@implementation SpotlightTextContentRetriever

static NSArray* mdimporterArray = nil;
static NSArray* unloadedMDImporterArray = nil;

static NSDictionary* contentTypesForMDImporter = nil;

static NSMutableDictionary* converters_ = nil;
static int osversion = 0;

+ (void)initialize
{
    if ( self == [SpotlightTextContentRetriever class] ) {
	
		converters_ = [[NSMutableDictionary alloc] init];
		osversion = [self OSVersion];
		
		if( mdimporterArray == nil )
			[self loadPlugIns];
    }
}

+(BOOL)loadPlugIns
{
	
	// Get and store MDImporter list	
	NSTask *task = [[NSTask alloc] init];
	NSPipe *messagePipe = [NSPipe pipe];
	
	[task setLaunchPath:@"/usr/bin/mdimport"];
	[task setArguments:[NSArray arrayWithObjects: @"-L" ,nil]];
	
	
	[task setStandardError : messagePipe];				
	[task launch];
	[task waitUntilExit];
	
	
	
	NSData *messageData = messagePipe fileHandleForReading] availableData]; 
	
	
	[[NSString* message;
	message = [[[NSString alloc] initWithData:messageData
									 encoding:NSUTF8StringEncoding] autorelease];
	
	[task release];
	
	
	
	// Cut unwanted string
	
	NSRange firstReturn = [message rangeOfString:@"(\n"];
	if( firstReturn.location == NSNotFound )
	{
		mdimporterArray = [[NSArray array] retain];
	}else
	{
		
		NSString* arrayStr = [message substringFromIndex:  firstReturn.location ];
		
		
		// Convert string to array
		
		NSData* data = [arrayStr dataUsingEncoding:NSASCIIStringEncoding];
		NSMutableArray *array = [NSPropertyListSerialization propertyListFromData:data 
																 mutabilityOption:NSPropertyListMutableContainers
																		   format:nil errorDescription:nil];
		
		
		if( array == nil ){
			NSLog(@"Parse Error");
			return NO;
		}
		
		
		
		//sortMDImporterArray
		NSMutableArray* sortedArray = [NSMutableArray array];
		int hoge;
		
		//(1) ~/Library/Spotlight/
		NSString* userFolder = [NSHomeDirectory() stringByAppendingPathComponent:@"Library/Spotlight"];
		
		for( hoge = 0; hoge < [array count]; hoge++ )
		{
			NSString* aStr = [array objectAtIndex:hoge];
			if( [aStr hasPrefix: userFolder] )
			{
				[sortedArray addObject: aStr];
			}
		}
		
		
		//(2) /Library/Spotlight/
		NSString* pluginFolder = @"/Library/Spotlight/";
		for( hoge = 0; hoge < [array count]; hoge++ )
		{
			NSString* aStr = [array objectAtIndex:hoge];
			if( [aStr hasPrefix: pluginFolder] )
			{				
				[sortedArray addObject: aStr];
			}
		}
		
		//(3) /System/Library/Spotlight/
		NSString* sysPluginFolder = @"/System/Library/Spotlight";
		for( hoge = 0; hoge < [array count]; hoge++ )
		{
			NSString* aStr = [array objectAtIndex:hoge];
			if( [aStr hasPrefix: sysPluginFolder] )
			{				
				[sortedArray addObject: aStr];
			}
		}
		
		
		//(4) others
		[array removeObjectsInArray: sortedArray];
		[sortedArray addObjectsFromArray: array];
		
		
		
		// exclude dynamic plugins ... check CFPlugInDynamicRegistration
		NSMutableArray* unloadedArray = [NSMutableArray array];
		
		for( hoge = 0; hoge < [sortedArray count]; hoge++ )
		{
			NSString* aStr = [sortedArray objectAtIndex:hoge];
			
			CFTypeRef dynamicValue = [[NSBundle bundleWithPath:aStr] objectForInfoDictionaryKey: @"CFPlugInDynamicRegistration"];
			
			
			BOOL removeFlag = NO;
			
			
			if( CFGetTypeID(dynamicValue) == CFBooleanGetTypeID()  )
			{
				
				removeFlag = CFBooleanGetValue(dynamicValue);
				
				
			}else if( CFGetTypeID(dynamicValue) == CFStringGetTypeID()  )
			{
				
				removeFlag = ( dynamicValue lowercaseString] isEqualToString:@"yes"] ? YES:NO);
			}
			
			
			if( removeFlag )
			{		
				[unloadedArray addObject:aStr];
				[sortedArray removeObjectAtIndex:hoge];
			}
			
			
		}
		
		mdimporterArray = [[[[NSArray alloc] initWithArray:sortedArray];
		unloadedMDImporterArray = [[NSArray alloc] initWithArray:unloadedArray];
		
		
		// Read value for key:CFBundleDocumentTypes in Info.plist
		// Get one item and check if it has key:CFBundleTypeRole value:MDImporter  
		// Read the value for key:LSItemContentTypes
		// set the value to contentTypesForMDImporter 
		
		NSMutableDictionary* contentTypesForMDImporterMutableDictionary = [[NSMutableDictionary alloc] init];
		
		for( hoge = 0; hoge < [mdimporterArray count]; hoge++ )
		{
			NSString* aStr = [mdimporterArray objectAtIndex:hoge];
			
			id value = [[NSBundle bundleWithPath:aStr] objectForInfoDictionaryKey: @"CFBundleDocumentTypes"];
			if( value != nil )
			{
				if( [value isKindOfClass:[NSArray class]] && [value count] >0 )
				{
					int piyo=0;
					for( piyo = 0; piyo < [value count] ; piyo++ )
					{
						id typeDictionary = [value objectAtIndex:piyo];
						if( [typeDictionary isKindOfClass:[NSDictionary class]] && 
							typeDictionary valueForKey:@"[[CFBundleTypeRole"] isEqualToString: @"MDImporter"]   )
						{
							id array = [typeDictionary objectForKey: @"LSItemContentTypes"];
							if( array != nil && [array isKindOfClass:[NSArray class]] )
							{
								if( [contentTypesForMDImporterMutableDictionary objectForKey: aStr] == nil )
									[contentTypesForMDImporterMutableDictionary setObject:[NSMutableArray array] forKey:aStr];
								
								contentTypesForMDImporterMutableDictionary objectForKey: aStr] addObjectsFromArray: array ];
							}
						
						}
					
					}
				
				}
			
			}
			
		}

		contentTypesForMDImporter = [[[[NSDictionary alloc] initWithDictionary:contentTypesForMDImporterMutableDictionary];
		
	}	
	
	return YES;
}

+(NSArray* )loadedPlugIns
{
	return mdimporterArray;
}
+(NSArray* )unloadedPlugIns
{
	return unloadedMDImporterArray;
}

+(NSDictionary*)contentTypesForMDImporter
{
	return contentTypesForMDImporter;
}

+(NSMutableArray* )metaDataOfFileAtPath:(NSString*)targetFilePath
{
	//Check plugIn list
	if( mdimporterArray == nil )
	{
		if( ![SpotlightTextContentRetriever loadPlugIns] ) 
			return [NSMutableArray array];
	}

	
	// Get UTI of the given file
	
	NSString* targetFilePath_converted = [targetFilePath stringByAddingPercentEscapesUsingEncoding: NSUTF8StringEncoding];
	NSURL* anUrl = [NSURL URLWithString: targetFilePath_converted];
	FSRef ref;
	CFURLGetFSRef(anUrl,&ref);
	CFTypeRef outValue;
	LSCopyItemAttribute (
						 &ref,
						 kLSRolesAll,
						 kLSItemContentType,
						 &outValue
						 );
	
	if( outValue == nil ) return nil;
	
	NSString* uti = [NSString stringWithString:outValue];
	CFRelease(outValue);
	
	//mdimporterArray
	//contentTypesForMDImporter

	
	
	int hoge;
	for (hoge = 0; hoge < [mdimporterArray count]; hoge++) {
		NSString* mdimporterPath = [mdimporterArray objectAtIndex:hoge];
		
		NSArray* contentUTITypes = [contentTypesForMDImporter objectForKey:mdimporterPath ];
		
		if( [contentUTITypes containsObject:uti ] )
		{

			// found one mdimporter
			NSMutableDictionary* attributes = 
			[SpotlightTextContentRetriever executeMDImporterAtPath:mdimporterPath 
														   forPath:targetFilePath_converted
															   uti:uti];
			
				
			//In 10.5, text content created by SourceCode.mdimporter contains only minimum keywords.
			// Overwrite full text here.
			if( osversion >= 1050 && UTTypeConformsTo ( uti, kUTTypeSourceCode  )) 
			{
			   
			   NSString* contents = [SpotlightTextContentRetriever readTextAtPath: targetFilePath];
			   if ( contents) {
				   [attributes setObject:contents forKey:kMDItemTextContent];
								
			   }			   
			}

		   return attributes;
		}
	}
	
	

		
	
	/* Original idea and code.  Did not work on Leopard for source code.
	
	
	//----------------
	
	
	//Get handlers that can handle the file
	CFArrayRef ha = LSCopyAllRoleHandlersForContentType (
														 uti,
														 kLSRolesAll
														 );	
	NSArray* handlerArray;
	
	if( ha == nil )
	{
		return nil;
	}else
	{
		handlerArray = [NSArray arrayWithArray: ha];
		CFRelease(ha);
	}
	
	//----------------
	
	//Evaluate
	
	int hoge;
	for( hoge = 0; hoge < [mdimporterArray count]; hoge++ )
	{
		NSString* mdimporterPath = [mdimporterArray objectAtIndex:hoge];			
		NSBundle* bndl = [NSBundle bundleWithPath: mdimporterPath ];
		
		if( bndl != nil )
		{
			
			int piyo;
			for( piyo = 0; piyo < [handlerArray count]; piyo++ )
			{
				NSString* aHandler = [handlerArray objectAtIndex:piyo];
				
	
				if( [aHandler isEqualToString:[bndl bundleIdentifier] ] )
				{

					//NSLog(@"Reading using %@",mdimporterPath);
					// found one mdimporter
					NSMutableDictionary* attributes = 
					[SpotlightTextContentRetriever executeMDImporterAtPath:mdimporterPath 
																   forPath:targetFilePath
																	   uti:uti];
					
					
					if( [attributes objectForKey:kMDItemTextContent] != nil )
						return attributes;

				}
			}
			
		}else
		{
			//NSLog(@"bndl is null");
			
		}
		
	}	
	*/
	
	return nil;
	
}

+(NSString*)readTextAtPath:(NSString*)path
{
	// When handling only ascii text, the source can be much more simple.
	
	
	
	NSData* data = [NSData dataWithContentsOfFile: path ];
	if ( data) {
		
		// Detect Encoding
		NSStringEncoding encoding;
		encoding = [JapaneseString detectEncoding: data];
		
		// Convert Encoding
		NSString* contents = nil;
		if (encoding == NSUnicodeStringEncoding ||
			encoding == CFStringConvertEncodingToNSStringEncoding(kCFStringEncodingUTF16BE) ||
			encoding == CFStringConvertEncodingToNSStringEncoding(kCFStringEncodingUTF16LE)) {
			contents = [[[NSString alloc] initWithData: data
											  encoding: encoding] autorelease];
		} else {
			TECConverter* converter = [SpotlightTextContentRetriever createConverter:encoding];
			contents = [converter convertToString: data];
		}
		return contents;
	}

	return nil;
}

+(int)OSVersion
{
	long SystemVersionInHexDigits;
	long MajorVersion, MinorVersion, MinorMinorVersion;
	
	Gestalt(gestaltSystemVersion, &SystemVersionInHexDigits);
	
	
	MinorMinorVersion = SystemVersionInHexDigits & 0xF;
	
	MinorVersion = (SystemVersionInHexDigits & 0xF0)/0xF;
	
	MajorVersion = ((SystemVersionInHexDigits & 0xF000)/0xF00) * 10 +
	(SystemVersionInHexDigits & 0xF00)/0xF0;
	
	
	////NSLog(@"ver %ld", SystemVersionInHexDigits);
	////NSLog(@"%ld.%ld.%ld", MajorVersion, MinorVersion, MinorMinorVersion);	
	
	
	return (int)MajorVersion*100 + MinorVersion*10 + MinorMinorVersion ;
}





+(NSString* )textContentOfFileAtPath:(NSString*)targetFilePath
{
	NSMutableDictionary* attributes = 
	[SpotlightTextContentRetriever metaDataOfFileAtPath:targetFilePath];
	
	id textContent = [attributes objectForKey:kMDItemTextContent];
	if( [textContent isKindOfClass:[NSString class]]  )
	{
		return textContent;
	}
	
	return nil;
}


+(NSMutableDictionary*)executeMDImporterAtPath:(NSString*)mdimportPath forPath:(NSString*)path uti:(NSString*)uti
{
	
	mdimportPath = [mdimportPath stringByAddingPercentEscapesUsingEncoding: NSUTF8StringEncoding];
	
	NSMutableDictionary* attributes = nil;
	CFBundleRef		bundle;
	
		

	CFURLRef url = CFURLCreateWithString (
											 nil,
											 mdimportPath,
											 nil
											 );
	 
	if( url == nil )	return nil;


	// Create CFPlugInRef
		
	CFPlugInRef plugin = CFPlugInCreate(NULL, url);
	CFRelease(url);


	
	if (!plugin)
	{
		//NSLog(@"Could not create CFPluginRef.\n");
		return nil;
	}

	
	//  The plug-in was located. Now locate the interface.

	BOOL foundInterface = NO;
	CFArrayRef	factories;
	
	//  See if this plug-in implements the Test type.
	factories	= CFPlugInFindFactoriesForPlugInTypeInPlugIn( kMDImporterTypeID, plugin );
	
	
	
	//  If there are factories for the Test type, attempt to get the IUnknown interface.
	if ( factories != NULL )
	{
		CFIndex	factoryCount;
		CFIndex	index;
		
		factoryCount	= CFArrayGetCount( factories );
		

		
		if ( factoryCount > 0 )
		{
			for ( index = 0 ; (index < factoryCount) && (foundInterface == false) ; index++ )
			{
				CFUUIDRef	factoryID;
				
				//  Get the factory ID for the first location in the array of IDs.
				factoryID = (CFUUIDRef) CFArrayGetValueAtIndex( factories, index );
				if ( factoryID )
				{
					
					IUnknownVTbl **iunknown;
					
					//  Use the factory ID to get an IUnknown interface. Here the plug-in code is loaded.
					iunknown	= (IUnknownVTbl **) CFPlugInInstanceCreate( NULL, factoryID, kMDImporterTypeID );
					
					if ( iunknown )
					{
						//  If this is an IUnknown interface, query for the test interface.
						(*iunknown)->QueryInterface( iunknown, CFUUIDGetUUIDBytes( kMDImporterInterfaceID ), (LPVOID *)( &mdimporterInterface ) );
						
						// Now we are done with IUnknown
						(*iunknown)->Release( iunknown );
						
						if ( mdimporterInterface )
						{
							//	We found the interface we need
							foundInterface	= true;
						}
					}
				}
			}
		}
		
		
		CFRelease( factories );

	}

	

		
	if ( foundInterface == false )
	{
	}
	else
	{
		attributes = [NSMutableDictionary dictionary];	


		/* This sometimes fails and causes crash. */
			
		@try {
			path = [path stringByReplacingPercentEscapesUsingEncoding: NSUTF8StringEncoding];

			
			//NSLog(@"GetMetadataForFile" );

			(*mdimporterInterface)->GetMetadataForFile( mdimporterInterface, 
														attributes, 
														uti,
														path);	
		
			//void* ptr = (*mdimporterInterface)->GetMetadataForFile;
			//NSLog(@"%x",ptr);
			//(*mdimporterInterface)->Release( mdimporterInterface );
			
			
			
		}
		@catch (NSException *exception) {
			attributes = nil;
			//NSLog(@"exception" );

		}
		
		


	}
	
	// Finished

	CFRelease( plugin );
	plugin	= NULL;
	
	return attributes;
}
#pragma mark Japanese Converter
+(TECConverter*)createConverter:(NSStringEncoding) encoding
{
TECConverter* converter;

converter = [converters_ objectForKey: [NSNumber numberWithInt: encoding]];

if (! converter) {
converter = [[TECConverter alloc] initWithEncoding: encoding];
[converters_ setObject: converter
			   forKey: [NSNumber numberWithInt: encoding]];
[converter release];
}

return converter;
}
@end




