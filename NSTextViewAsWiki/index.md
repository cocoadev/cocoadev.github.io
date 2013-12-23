---
layout: page
title: NSTextViewAsWiki
---

Seeking a bit of advice: I'm just diving into the NSTextView and I'd like to write a small wiki type system (very similar to VoodooPad actually but with a few twists).  I believe I get how I'd actually convert all Wiki Names to links, but I'm not real sure where in the pipeline to place this code.  I'm assuming for efficiency this should really by done by watching text as it's typed and adding the link attributes as needed.  Which delegate would be appropriate for basically watching the text as it is typed?  Or, is there a better way?

Thanks,

EricFreeman

----

I'll let the code speak for itself... (PS WikiController is the delegate of it's wikiTextView and wikiWindow, also the data source of it's wikiLocation combo box)

    
/* WikiController */

#import <Cocoa/Cocoa.h>
#import "WikiManager.h"

@interface WikiController : NSObject
{
    IBOutlet WikiManager *wikiManager;
    IBOutlet NSWindow *wikiWindow;
	IBOutlet NSComboBox* wikiLocation;
	IBOutlet NSTextView *wikiTextView;
}
- (IBAction)changeWikiLocation:(id)sender;
- (IBAction)removeCurrentNode:(id)sender;
-(void)scanForWikiWords;
@end

@interface WikiController (NSTextViewDelegate)
-(void)textDidChange:(NSNotification*)aNotification;
- (BOOL)textView:(NSTextView *)aTextView 
    clickedOnLink:(id)link 
    atIndex:(unsigned)charIndex;
@end

@interface WikiController (NSComboBoxDataSource)
-(int)numberOfItemsInComboBox:(NSComboBox*)aComboBox;
-(id)comboBox:(NSComboBox*)aComboBox 
    objectValueForItemAtIndex:(int)index;
@end

@interface WikiController (NSWindowDelegate)
-(void)windowWillClose:(NSNotification *)aNotification;
@end

//WikiController.m
#import "WikiController.h"

@implementation WikiController

- (IBAction)changeWikiLocation:(id)sender
{
	NSString* wikiKey  = [wikiLocation stringValue]; 
	NSString* wikiText = nil;
	
	if([wikiManager isValidWikiKey:wikiKey]) {
		wikiText = [wikiManager wikiTextForWikiKey:wikiKey];
		wikiText ? 
                    [wikiTextView setString:wikiText] 
                    : [wikiTextView setString:@""];
		[self scanForWikiWords];
	} else {
		[wikiTextView setString:@"Invalid Key"];
	}
}

- (IBAction)removeCurrentNode:(id)sender
{
	NSString* wikiKey  = [wikiLocation stringValue];
	
	[wikiLocation setStringValue:@"SandBox"];
	[self changeWikiLocation:self];
	[wikiManager removeWikiKey:wikiKey];
	[wikiLocation noteNumberOfItemsChanged];
	
}

-(void)scanForWikiWords
{
	NSString*  wikiText = [wikiTextView string];
	NSScanner* wikiScan = [[NSScanner alloc] initWithString:wikiText];
	NSString*  currentWord = nil;
	NSRange    currentRange;
	NSTextStorage* wikiStorage = [wikiTextView textStorage];
	
	[wikiStorage removeAttribute:NSLinkAttributeName 
                             range:NSMakeRange(0,[wikiText length])];
	
	NSCharacterSet* alphaChars = [NSCharacterSet alphanumericCharacterSet];
	
	[wikiScan scanUpToCharactersFromSet:alphaChars intoString:nil];
	while(![wikiScan isAtEnd]) {
		[wikiScan scanCharactersFromSet:alphaChars intoString:&currentWord];
		
		if([wikiManager isValidWikiKey:currentWord]) {
			currentRange.location = [wikiScan scanLocation] - [currentWord length];
			currentRange.length   = [currentWord length];

			[wikiStorage setAttributes: // on link clicked pass the WikiWord
                              [NSDictionary dictionaryWithObject:currentWord forKey:NSLinkAttributeName] 
                        range:currentRange];
			
		}
		[wikiScan scanUpToCharactersFromSet:alphaChars intoString:nil];
	}
	return;
}
@end

@implementation WikiController (NSTextViewDelegate)
-(void)textDidChange:(NSNotification*)aNotification
{
	//optimize by only checking the words at the index of the change
	
	[self scanForWikiWords];
	NSString* wikiKey = [wikiLocation stringValue];
	NSString* wikiText = [wikiTextView string];
	
	[wikiManager setWikiText:wikiText forWikiKey:wikiKey];
	[wikiLocation noteNumberOfItemsChanged];
}
- (BOOL)textView:(NSTextView *)aTextView clickedOnLink:(id)link atIndex:(unsigned)charIndex
{
	[wikiLocation setStringValue:link];
	[self changeWikiLocation:self];
	return YES;
}
@end

@implementation WikiController (NSComboBoxDataSource)
-(int)numberOfItemsInComboBox:(NSComboBox*)aComboBox
{
	NSArray* wikiKeys = [wikiManager wikiKeys];
	return [wikiKeys count];
}

-(id)comboBox:(NSComboBox*)aComboBox objectValueForItemAtIndex:(int)index
{
	NSArray* wikiKeys = [wikiManager wikiKeys];
	return [wikiKeys objectAtIndex:index];
}
@end

@implementation WikiController (NSWindowDelegate)
-(void)windowWillClose:(NSNotification *)aNotification
{
	[NSApp terminate:self];
}
@end

/* WikiManager.h */

#import <Cocoa/Cocoa.h>

@interface WikiManager : NSObject
{
}
-(NSArray*)wikiKeys;
-(NSString*)wikiTextForWikiKey:(NSString*)aWikiKey;
-(void)setWikiText:(NSString*) newText forWikiKey:(NSString*)aWikiKey;
-(BOOL)isValidWikiKey:(NSString*)aWikiKey;
-(void)removeWikiKey:(NSString*)aWikiKey;
@end

@interface WikiManager (WikiData)
-(NSDictionary*)wikiData;
-(void)setWikiData:(NSDictionary*)newWikiData;
@end

@interface NSString (McWordAtIndex)
-(NSString*)wordAtIndex:(int)anIndex;
@end

//WikiManager.m
#import "WikiManager.h"

@implementation WikiManager
-(NSArray*)wikiKeys
{
	NSDictionary * wikiData = [self wikiData];
	return [wikiData allKeys];
}

-(NSString*)wikiTextForWikiKey:(NSString*)aWikiKey
{
	NSDictionary * wikiData = [self wikiData];
	return [wikiData objectForKey:aWikiKey];
}

-(void)setWikiText:(NSString*) newText forWikiKey:(NSString*)aWikiKey
{
	NSDictionary * wikiData = [self wikiData];
	NSMutableDictionary* mutableWikiData = 
                 [NSMutableDictionary dictionaryWithDictionary:wikiData];
	[mutableWikiData setObject:newText forKey:aWikiKey];
	[self setWikiData:mutableWikiData];
	return;
}

-(BOOL)isValidWikiKey:(NSString*)aWikiKey
{
	if(!aWikiKey) return NO;
	if([aWikiKey length] < 2) return NO;
	//check for no illegal characters (whitespace, punctuation...) initial caps
	BOOL noIllegalChars = aWikiKey wordAtIndex:0] isEqual:aWikiKey];
	BOOL initialCaps = [[[[NSCharacterSet uppercaseLetterCharacterSet] 
			characterIsMember:[aWikiKey characterAtIndex:0]];
	//check for at least one more capital
	NSScanner* wikiScan = [[NSScanner alloc] initWithString:aWikiKey];
	[wikiScan setScanLocation:1];
	[wikiScan scanUpToCharactersFromSet:
                 [NSCharacterSet uppercaseLetterCharacterSet] 
        intoString:nil];
	BOOL secondCaps = ![wikiScan isAtEnd];
	return noIllegalChars && initialCaps && secondCaps;
}

-(void)removeWikiKey:(NSString*)aWikiKey
{
	NSDictionary* wikiData = [self wikiData];
	NSMutableDictionary* mutableWikiData = 
                 [NSMutableDictionary dictionaryWithDictionary:wikiData];
	[mutableWikiData removeObjectForKey:aWikiKey];
	[self setWikiData:mutableWikiData];
}

@end

@implementation WikiManager (WikiData)
-(NSDictionary*)wikiData
{
	NSDictionary* wikiData = 
                [[NSUserDefaults standardUserDefaults] 
                              objectForKey:@"WikiData"];
	if(!wikiData) {
		wikiData = [[NSDictionary alloc] init];
		[self setWikiData:wikiData];
	}
	return wikiData;
}

-(void)setWikiData:(NSDictionary*)newWikiData
{
	[[NSUserDefaults standardUserDefaults] 
                 setObject:newWikiData forKey:@"WikiData"];
	return;
}
@end

@implementation NSString (McWordAtIndex)
-(NSString*)wordAtIndex:(int)anIndex
{
	NSString * result = nil;
	NSCharacterSet* goodChars = [NSCharacterSet alphanumericCharacterSet];
	
	if([goodChars characterIsMember:[self characterAtIndex:anIndex]])
	{
		NSRange resultRange;
		resultRange.location = anIndex;
		while(
                        resultRange.location-- != 0 && 
                        [goodChars characterIsMember:
                                 [self characterAtIndex:resultRange.location]]
                );
		resultRange.location++;
		resultRange.length = anIndex;
		while(
                      (++resultRange.length) < [self length] && 
                      [goodChars characterIsMember:
                                  [self characterAtIndex:resultRange.length]]
                );
		resultRange.length -= resultRange.location;
		result = [self substringWithRange:resultRange];
	}
	
	return result;
}
@end


The end result could be much more optimized with creating the attributes... but it runs great on my G3 iBook for now (maybe when it gets to have large entries and many entries I'll need to play with things)... at any rate, I'll go with Knuth: "Premature optimization is the root of all evil"

