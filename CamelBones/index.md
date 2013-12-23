---
layout: page
title: CamelBones
---



ObjC to PerlLang bridge. See http://www.camelbones.org/.

Written and maintained by ShermPendley.  After CamelBones 1.0.3, Sherm took a long hiatus, during which CB went essentially unmaintained and supported neither Leopard nor Snow Leopard. Sherm returned to active development in 2009, and CamelBones 1.1 was released in November of 2009 with support for Panther, Tiger, Leopard, and Snow Leopard.

----

Here's an ok example, but you have to make your own project and interface in IB.
1 - Download and install camelbones.
2 - Add the camelbones framework to your project.
3 - instantiate an instance (sic) of Controller in IB
4 - add the three interface elements specified in the header to your IB project, and connect them accordingly.
5 - run

    

#import <Cocoa/Cocoa.h>
#import <CamelBones/CamelBones.h>

@interface Controller : NSObject
{
	//Make all three of these in your window and connect controller to them
	IBOutlet id textField; // For entering the URL; connect action to getHTML
	IBOutlet id tableView; // Connect datasource to controller
       IBOutlet id textView;
    
	NSMutableArray *downloads;
       CBPerl *perlObject;
}

- (IBAction)getHTML:(id)sender;
- (IBAction)displayHTML:(NSString *)displayString;
- (void)parseHTML:(NSString *)htmlString;
- (void)setDownloads;

@end



    

#import "Controller.h"

@implementation Controller

#pragma mark -
#pragma mark Designated Initializer

-(id)init
{
	self = [super init];
	
	if ( self ) {		
		downloads = [[NSMutableArray alloc] init];
		perlObject = [[CBPerl alloc] init];
			[perlObject useModule: @"HTML::LinkExtor"];
	}
	
	return self;
}

#pragma mark -
#pragma mark IB Actions

- (IBAction)getHTML:(id)sender
{
	int encoding = 1; // Not neccessarily the best value
	NSError *error;
	NSString *html = [NSString stringWithContentsOfURL:[NSURL URLWithString:[sender stringValue]] encoding:encoding error:&error];

	if ( html ) {
		[self displayHTML:html];
		[self parseHTML:html];
		[self setDownloads];
	} else {
		NSLog(@"Load failed with error %@", [error localizedDescription]);
	}
}

- (IBAction)displayHTML:(NSString *)displayString
{
    [textView setString:displayString];
}

#pragma mark -
#pragma mark Core functions illustrating embedded CamelBones perl

- (void)parseHTML:(NSString *)htmlString
{
	[perlObject setValue:htmlString forKey:@"string"];
	[perlObject eval:@"$parser = HTML::LinkExtor->new;"];
	[perlObject eval:@"$parser->parse($string);"];
}

- (void)setDownloads;
{
	[perlObject eval:@"@links = $parser->links;"];
	
	CBPerlArray *links;
		links = [perlObject namedArray:@"links"];
	
	//[downloads removeAllObjects];
	[downloads addObjectsFromArray:links];
	
    [tableView reloadData];
}

#pragma mark -
#pragma mark TableView datasource methods

- (id)tableView:(NSTableView *)aTableView objectValueForTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
{
    return [downloads objectAtIndex:rowIndex];
}

- (int)numberOfRowsInTableView:(NSTableView *)aTableView
{
    return [downloads count];
}

@end



-- Stephen

