---
layout: page
title: NSWorkspaceOpenURLProblems
---



Hi,
I'm modifying my WebView subclass (DXWebView) so I can display custom menus for links. The menu is created, displayed correctly and all, but I can't get NSWorkspace actions on the subclass to worl, here is the code:

DXWebView.h
    
/* DXWebView */

#import <Cocoa/Cocoa.h>
#import <WebKit/WebView.h>
#import <WebKit/WebUIDelegate.h>

@interface DXWebView : WebView
{
	NSString *urlLink;
	
	NSMenuItem *menuOpenSafari;
	NSMenuItem *menuDownloadAttachment;
}

@end


DXWevView.m
    
@implementation DXWebView

- (void)awakeFromNib
{	
	[self setUIDelegate:self];
	
	menuOpenSafari = [[NSMenuItem alloc] init];
	[menuOpenSafari setTitle:@"Open Link With Safari"];
	[menuOpenSafari setEnabled:YES];
	[menuOpenSafari setAction:@selector(openWithSafari)];
	[menuOpenSafari setTarget:self];
	
	menuDownloadAttachment = [[NSMenuItem alloc] init];
	[menuDownloadAttachment setTitle:@"Download Attached File"];
	[menuDownloadAttachment setEnabled:YES];
	[menuDownloadAttachment setAction:@selector(downloadFile)];
	[menuDownloadAttachment setTarget:self];
}

- (NSArray *)webView:(WebView *)sender contextMenuItemsForElement:(NSDictionary *)element defaultMenuItems:(NSArray *)defaultMenuItems
{
	sender = self;
	if (element objectForKey:[[WebElementLinkURLKey] isNotEqualTo:nil])
	{
		urlLink = nil;
		urlLink = [element objectForKey:WebElementLinkURLKey];		
		defaultMenuItems = [[NSArray alloc] initWithObjects:menuOpenSafari, menuDownloadAttachment,  nil];
	}
	return defaultMenuItems;
}

- (void)openWithSafari
{
	[[NSWorkspace sharedWorkspace] openURL:[NSURL URLWithString:urlLink]];
}

- (void)downloadFile
{
	[[[NSAppleScript alloc]
        initWithSource:[NSString stringWithFormat:@"do shell script \"open %@\"", urlLink]]
        executeAndReturnError:nil];
}

@end


So, when i load an html file and control-click on the link, the menu with the two menu items declared and set there are displayed. But when I click the one that calls openWithSafari, I get this:

    
2005-09-25 11:11:47.296 Flexus[477] *** -[NSURL length]: selector not recognized
2005-09-25 11:11:47.397 Flexus[477] *** -[NSURL length]: selector not recognized


I don't know why that happens, when I log urlLink, the string is fine, but somehow the URL created from it gets messed up.
Any ideas? Thank you in advance -FernandoLucasSantos

----

The object for     WebElementLinkURLKey is an NSURL, but you're treating it like an NSString. Hence,     [NSURL URLWithString:urlLink] will fail, because     urlLink is *already* an NSURL.

----
*feels dumb* - Thank you very much!

