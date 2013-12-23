---
layout: page
title: NSXMLDocumentErrors
---

Big mistake on my part...duh!  I was releasing the NSURL, NSXMLDocument, and error...I should not be releasing those, as I did not allocate the memory...caused the application to crash... *Except the NSXMLDocument...don't forget to release that. --JediKnil*
----
I have a a very simple method that parses an XML file.  The parse of the XML causes a "sigterm 11" and my app crashes.  Below is the method that is executed:  I looked at Apple's documentation for NSXMLDocument, and I've
adapted their code sample here.  Any help/suggestions would be greatly appreciated. 

     
- (IBAction)parseXMLConfig:(id)sender
{
    NSString *file = @"/path-to/config.xml";
    NSXMLDocument *xmlDoc;
    NSError *err = nil;
    NSURL *furl = [NSURL fileURLWithPath:file];
    if (!furl) {
        NSLog(@"Can't create an URL from file %@.", file);
        return;
    }
    xmlDoc = [[NSXMLDocument alloc] initWithContentsOfURL:furl
												  options:(NSXMLNodePreserveWhitespace|NSXMLNodePreserveCDATA)
												  error:&err];
    //NSLog(@"XMLDocument: %@\n", xmlDoc);
    if (xmlDoc == nil) {
        xmlDoc = [[NSXMLDocument alloc] initWithContentsOfURL:furl
												  options:NSXMLDocumentTidyXML
	                  									  error:&err];
    }
    if (xmlDoc == nil)  {
        if (err) {
            NSLog(@"Handle an error");
        }
        return;
    }        
    if (err) {
		NSLog(@"Handle an error");
        
    }

	[furl release];
	[xmlDoc release];
	[err release];
	[file release];
}
 

*The only thing that should be released in the above code is xmlDoc. Everything else is autoreleased, meaning you don't release it.*

