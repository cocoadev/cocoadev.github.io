---
layout: page
title: DOMCore
---



Cocoa's DOM (DocumentObjectModel) implementation.

http://developer.apple.com/documentation/Cocoa/Conceptual/DisplayWebContent/index.html

Classes:
[Topic]

----

The DOMDocument, DOMNodeList, DOMNamedNodeMap and DOMNode form the foundation for building HTML object trees. WebFrame's method     DOMDocument returns the root node for the frame's NSURLRequest. You can walk through the document tree starting with this node to examine all of the page's leafs.

    
- (void)walkNodeTree:(DOMNode *)parent {
	DOMNodeList *nodeList = [parent childNodes];
	unsigned i, length = [nodeList length];
	for (i = 0; i < length; i++) {
		DOMNode *node = [nodeList item:i];
		[self walkNodeTree:node];
		DOMNamedNodeMap *attributes = [node attributes];
		unsigned a, attCount = [attributes length];
		NSMutableString *nodeInfo = [NSMutableString stringWithCapacity:0];
		NSString *nodeName = [node nodeName];
		NSString *nodeValue = [node nodeValue];
		[nodeInfo appendFormat:@"node[%i]:\nname: %@\nvalue: %@\nattributes:\n", 
								i, nodeName, nodeValue];
		for (a = 0; a < attCount; a++) {
			DOMNode *att = [attributes item:a];
			NSString *attName = [att nodeName];
			NSString *attValue = [att nodeValue];
			[nodeInfo appendFormat:@"\tatt[%i] name: %@ value: %@\n", a, attName, attValue];
		}		
		NSLog(nodeInfo);
	}
}

- (void)webView:(WebView *)sender didFinishLoadForFrame:(WebFrame *)frame {
	WebDataSource *dataSource = [frame dataSource];
	NSArray *subresources = [dataSource subresources];
	DOMDocument *doc = [frame DOMDocument];
	[self walkNodeTree:[doc documentElement]];
}

 

You can edit HTML directly through Apple's DOM implementation by setting the supporting WebView to editable (    [webView setEditable:YES];). Apple's documentation is kind of lean right now, so hopefully more can be posted here.

--zootbobbalu

----

I want to set the values of specific HTML form fields in a web view. It seems like accessing the DOM is the way to do this (I don't see any hooks in the WebView APIs) but I just can't make it work. I started by finding the DOMNode I want to set using the walkNodeTree code above and then tried to manipulate a node using methods from http://developer.apple.com/documentation/Darwin/Reference/ManPages/mann/domNode.n.html. 

    
// inside the for() look in walkNodeTree, find a node with a "value" attribute (in my case, an input type=text)
if( [attName isEqualToString:@"value"] ){
    // warning: DOMNode does not respond to setAttribute
    [node setAttribute:@"value" :@"blah"];// attributeName newValue

    // this has no effect, probably b/c it doesn't set an attribute, but the "value"
    [node setNodeValue:@"blah"];

    // this is the approach used in apple's DOMHTMLEditor example, except it still has no effect
    DOMNode *newNode = [parent replaceChild:node:[parent firstChild]];
}


There must be a way to do this, or else how would Safari's "Remember password" feature work? 

Thanks, Robert

