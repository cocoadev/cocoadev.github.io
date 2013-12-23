---
layout: page
title: WritingXMLFiles
---

There are four ways to write XML on OS X:


* General/XmlPropertyLists by calling writeToFile:atomically:
* using a third party library such as Xerces, General/IconaraDOM (see bottom for code example) or General/XMLTree
* using Apple's General/CFXMLParser API
* since Tiger, using Apple's NSXML API


This article explains the third method, using General/CFXMLParser in the General/CoreFoundation API.  Here is the XML document we will be creating:

    
<?xml version="1.0" encoding="utf-8"?>

<person>
	<name> Alan Turing </name>
	<website href="http://turing.org" />
</person>


The above XML is made up of a tree of General/CFXMLNode objects.  The particular type of node is based on General/CFXMLNodeTypeCode.  The following is a break down of the various types used in the example:

    
1)  kCFXMLNodeTypeDocument
2)      kCFXMLNodeTypeProcessingInstruction -- "<?xml version="1.0" encoding="utf-8"?>"
3)      kCFXMLNodeTypeElement -- "<person>"
4)         kCFXMLNodeTypeElement -- "<name>"
5)            kCFXMLNodeTypeText -- "Alan Turing"
6)            kCFXMLNodeTypeElement -- "<website href="http://turing.org" />"


** 1) Document **

We begin by creating a document node.  All further nodes will be added as children to this node (xmlDocument).

    
	General/CFXMLDocumentInfo documentInfo;
	documentInfo.sourceURL = NULL;
	documentInfo.encoding = kCFStringEncodingUTF8;
	General/CFXMLNodeRef docNode = General/CFXMLNodeCreate(
		kCFAllocatorDefault,
		kCFXMLNodeTypeDocument,
		CFSTR(""),
		&documentInfo,
		kCFXMLNodeCurrentVersion);
	General/CFXMLTreeRef xmlDocument = General/CFXMLTreeCreateWithNode(kCFAllocatorDefault, docNode);
	General/CFRelease(docNode);


** 2) Instruction Tag **

The first line of our document needs to be the <?xml /> processing instruction.  Here's how we create and add it to the document.

    
	General/CFXMLProcessingInstructionInfo instructionInfo;
	instructionInfo.dataString = CFSTR("version=\"1.0\" encoding=\"utf-8\"");
	General/CFXMLNodeRef instructionNode = General/CFXMLNodeCreate(
		NULL, 
		kCFXMLNodeTypeProcessingInstruction,
		CFSTR("xml"),
		&instructionInfo,
		kCFXMLNodeCurrentVersion);
	General/CFXMLTreeRef instructionTree = General/CFXMLTreeCreateWithNode(kCFAllocatorDefault, instructionNode);
	General/CFTreeAppendChild(xmlDocument, instructionTree);
	General/CFRelease(instructionTree);
	General/CFRelease(instructionNode);



** 3) Document's Root Element **

The <person> tag is the document's root element.  We'll later add two sub-elements to this node.  Notice how personInfo.isEmpty is set to NO.  This tell General/CFXMLParser that the tag will have child nodes added to it later.  If you forget to do this you output will look like this: * <person /> <name>Alan Turing </name> *.

    
	General/CFXMLElementInfo personInfo;
	personInfo.attributes = NULL;
	personInfo.attributeOrder = NULL;
	personInfo.isEmpty = NO;	
	General/CFXMLNodeRef personNode = General/CFXMLNodeCreate (
	   kCFAllocatorDefault,
	   kCFXMLNodeTypeElement,
	   (General/CFStringRef)@"person",
	   &personInfo,
	   kCFXMLNodeCurrentVersion);	
	General/CFXMLTreeRef personTree = General/CFXMLTreeCreateWithNode(kCFAllocatorDefault,personNode);
	General/CFTreeAppendChild(xmlDocument, personTree);
	General/CFRelease(personNode);



** 4) Name Sub-Element **

Now we add the name node.  The text it contains is a separate node we'll add next.

    
	General/CFXMLElementInfo nameInfo;
	nameInfo.attributes = NULL;
	nameInfo.attributeOrder = NULL;
	nameInfo.isEmpty = NO;	
	General/CFXMLNodeRef nameNode = General/CFXMLNodeCreate (
	   kCFAllocatorDefault,
	   kCFXMLNodeTypeElement,
	   (General/CFStringRef)@"name",
	   &nameInfo,
	   kCFXMLNodeCurrentVersion);	
	General/CFXMLTreeRef nameTree = General/CFXMLTreeCreateWithNode(kCFAllocatorDefault,nameNode);
	General/CFTreeAppendChild(personTree, nameTree);
	General/CFRelease(nameTree);
	General/CFRelease(nameNode);


** 5) Text Element **

The actual value of the name element is a separate node.

    
	General/CFXMLNodeRef nameTextNode = General/CFXMLNodeCreate (
	   kCFAllocatorDefault,
	   kCFXMLNodeTypeText,
	   (General/CFStringRef)@"Alan Turing",
	   NULL,
	   kCFXMLNodeCurrentVersion);	
	General/CFXMLTreeRef nameTextTree = General/CFXMLTreeCreateWithNode(kCFAllocatorDefault,nameTextNode);
	General/CFTreeAppendChild(nameTree, nameTextTree);
	General/CFRelease(nameTextTree);
	General/CFRelease(nameTextNode);


** 6) Adding Attributes **

In this node we'll add an attribute.  General/CFXMLElementInfo::attributes is a dictionary of attributes.  The key is the attribute's name as a string (in this case "href").  The object is the attribute's value (in this case "http://turing.org").  General/CFXMLElementInfo::attributeOrder is a list of all the attribute names in the same order as they should be written out in the final document.

    
	General/NSDictionary *attributeDictionary = General/[NSDictionary
	   dictionaryWithObject:@"http://turing.org" forKey:@"href"];
	General/NSArray *attributeArray = General/[NSArray arrayWithObject:@"href"];
	General/CFXMLElementInfo websiteInfo;
	websiteInfo.attributes = (General/CFDictionaryRef) attributeDictionary;
	websiteInfo.attributeOrder = (General/CFArrayRef) attributeArray;
	websiteInfo.isEmpty = YES;	
	General/CFXMLNodeRef websiteNode = General/CFXMLNodeCreate (
	   kCFAllocatorDefault,
	   kCFXMLNodeTypeElement,
	   (General/CFStringRef)@"website",
	   &websiteInfo,
	   kCFXMLNodeCurrentVersion);	
	General/CFXMLTreeRef websiteTree = General/CFXMLTreeCreateWithNode(kCFAllocatorDefault,websiteNode);
	General/CFTreeAppendChild(personTree, websiteTree);
	General/CFRelease(websiteTree);
	General/CFRelease(websiteNode);


** 7) Saving and Clean Up **

Now the final step, to write the document to disk.  This is achieved with the General/CFXMLTreeCreateXMLData function.  If you view the resulting xml file in a text editor you'll notice that all the output is on one line.

    
	General/CFDataRef xmlData = General/CFXMLTreeCreateXMLData(kCFAllocatorDefault, xmlDocument);
	BOOL result = [(General/NSData *)xmlData writeToFile:@"foo.xml" atomically:YES];
	General/NSAssert(result, @"Writing to file failed");
	General/CFRelease(xmlData);
	General/CFRelease(personTree);  // god kills a kitten every time you leak memory :-P
	General/CFRelease(xmlDocument);


--General/SaileshAgrawal

----

Another way of doing this is to use General/XMLTree, or the DOM framework (General/IconaraDOM). Both ways are considerably simpler, and, being the author of the DOM framework, I would say that it is the prefered way of doing it.

Now, to produce the same XML-document as in the example above, do this:

    
id <General/DOMDocument> document;
id <General/DOMElement> personElement, nameElement, websiteElement;
id <General/DOMText> text;
id <General/DOMAttribute> hrefAttribute;

// 1) Create a document
document = General/[DOMDocument document];

// 2) Instruction tag
// not necessary, will be added by the formatter

// 3) Document's root element
personElement = General/[DOMElement elementWithName:@"person"];
[document setDocumentElement:personElement]; 

// 4) Name subelemet
nameElement = General/[DOMElement elementWithName:@"name"];
[personElement appendChild:nameElement];

// 5) Text element
text = General/[DOMText textWithString:@"Alan Turing"];
[nameElement appendChild:text];

// 6) Adding attributes
websiteElement = General/[DOMElement elementWithName:@"website"];
hrefAttribute = General/[DOMAttribute attributeWithName:@"href" value:@"http://turing.org"];
[websiteElement addAttribute:hrefAttribute];
[personElement appendChild:websiteElement];

// 7) Saving and cleaning up
General/[DOMFormatter writeNode:document General/ToFile:@"foo.xml"];


Update: the adding the prolog is not necessary (and really an error), so that step has been removed.
Update: rewrote example to work with the v2.0 interface.

This is also the way it is done in other DOM-compliant frameworks, such as DOM, JDOM and XOM for Java. If you have worked with any of these, the DOM framework for Cococa (General/IconaraDOM) will look familiar. 
The DOM (Document Object Model) is the prefered way of working with XML-documents if you need to insert, remove and rearrange parts of it. This can also be done with CFXML, NSXML or General/XMLTree, but it is more cumbersome.

-- General/TheoHultberg/Iconara

----

General/XMLTree doesn't support creating XML, correct? Maybe I'll go ahead and do that :-)
