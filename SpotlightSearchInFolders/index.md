---
layout: page
title: SpotlightSearchInFolders
---



I have an app that uses the Spotlight API, but now I want to limit my search to certain folders e.g ~/Movies or ~/Photos

Is there a way to make Spotlight return just those in the directories directly or do I have to sort them myself afterwards?

-> You're searching for "Search Scope".

NSMetadataQuery (http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSMetadataQuery.html)
-(void)setSearchScopes:(NSArray *)scopes

<br>

inside of a "saved search" the XML for this looks like
      in search criteria - 
<key>FXScopeArrayOfPaths</key>
		<array>
			<string>/Users/(name)/(folder)</string>
		</array>

