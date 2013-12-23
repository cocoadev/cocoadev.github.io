---
layout: page
title: XmlFileReading
---



Hi,

are there some methods to easily read xml files and their nodes?
The same for writing them. I haven�t found anything yet.

Greetings

General/ThomasSempf

----

Hi General/ThomasSempf,
for getting an xml file into an array: General/[[NSArray alloc] initWithContentsOfFile: @"myinput.xml"].
for saving an array to an xml file: [anArray writeToFile: @"myoutput.xml" atomically: NO]

Then you can simply read/write the single elements within the array.
Hope it helps.

Bye
peacha

----

Be careful not to confuse XML files with General/XmlPropertyLists. An XML file can contain a variety of data in a variety of formats. General/XmlPropertyLists refers to a specific XML format to store arrays, strings, dictionaries, for General/ObjC and General/CoreFoundation applications.

Anyone know how you read and write arbitrary XML files? Can it be done with General/CoreFoundation?

-- General/MikeTrent

----

An General/NSArray reads/writes "pure" XML files, while an General/NSDictionary operates (with the same methods as General/NSArray) on property lists.
You can do it even with General/CoreFoundation: there is "Property List Services" for that.
In General/CoreFoundation/General/CFPropertyList.h there is function General/CFDataRef General/CFPropertyListCreateXMLData (General/CFAllocatorRef, General/CFDictionaryRef);

peacha

----

Eh? You're still talking about property lists, which can only support strings, arrays, dictionaries, numbers and booleans. In General/CoreFoundation you can use XML Services for generic XML parsing. See http://developer.apple.com/techpubs/macosx/General/CoreFoundation/General/XMLServices/xmlservices_carbon.html

*Apple's new layout is a bit different: you might want to try* http://developer.apple.com/documentation/General/CoreFoundation/Conceptual/CFXML/index.html *instead.*

-- General/FinlayDobbie

----

Hey, you're right Finlay. And wow, it even lets you customize the parser! I must admit that I am missing a lot of interesting things about General/CoreFoundation.

peacha

----

If you want a Framework for XML see here: http://www.subsume.com/static/General/WebObjects/General/SubsumeSite/General/SubsumeSite/Tech/Software/STXML.html

----

Also check out General/XMLTree, a Public Domain Objective-C wrapper for Apple's XML parser.

----

A real, DOM-compliant, XML framework is Iconara DOM Framework (General/IconaraDOM). It's similar to the Java frameworks JDOM (www.jdom.org) and XOM (www.xom.nu). What's more: it's GPL'ed. 

General/IconaraDOM: http://sourceforge.net/projects/iconaradom

----

I'm trying to use General/XMLTree to read an XML file. This is what I'm trying to read:

    
<?xml standalone="yes" version="1.0" ?>
<Preferences>
	<Registration>
		<Name>General/TestPerson</Name>
	</Registration>
</Preferences>


Now when I call General/[XMLTree description], it returns "General/TestPerson" (minus the quotes). When I call General/[XMLTree count], it returns 3. Now I wan't to read the "Name" item. Here's what I'm trying:

    
General/XMLTree *file = General/[XMLTree treeWithURL:[NSURL fileURLWithPath:[_HOME stringByAppendingString:_acpAlarmsPathComponent]]];
General/XMLTree *prefs = [file descendentNamed:@"Preferences"];
General/XMLTree *registration = [prefs descendentNamed:@"Registration"];
General/NSString *theName = [registration attributeNamed:@"Name"];


However, no matter what I do, theName is always nil. I can't figure it out. I was hoping someone here could help me out a little bit. Thanks.

*It looks like Name is a descendent, not an attribute.*

----

The same example with Iconara DOM (see above):

    
General/DOMDocument *document = General/[DOMBuilder documentFromFile:@"whicheverfile"];
General/DOMElement *prefs = [document rootElement];
General/DOMElement *reg = General/prefs children] objectAtIndex:0];
[[NSString *theName = General/[reg children] objectAtIndex:0] string];


or in v1.1 (comming soon)

    
[[DOMDocument *document = General/[DOMBuilder documentFromFile:@"whicheverfile"];
General/NSString *theName = General/document getElementsByTagName:@"name"] objectAtIndex:0];


or you could do it with an [[XPath filter.

-- General/TheoHultberg/Iconara

----

It appears the the General/CFXMLTree create functions don't like XML that defines variable numbers of child elements, e.g.

    
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE person [
<!ELEMENT first_name (#PCDATA)>
<!ELEMENT last_name (#PCDATA)>
<!ELEMENT profession (#PCDATA)>
<!ELEMENT name (first_name, last_name)>
<!ELEMENT person (name, profession*)>      <--- THIS LINE
]>
<person>
  <name>
    <first_name>Alan</first_name>
    <last_name>Turing</last_name>
  </name>
  <profession>computer scientist</profession>
  <profession>mathetician</profession>
  <profession>cryptographer</profession>
</person>


Note the * after the profession. I get a nil General/CFXMLTree if I put that in, but  if I take it out and make no other changes, the General/CFXMLTree is created. Is this to be expected? Is there something about General/CFXMLTrees that require a single child (or equal number of children) per node?
-- General/GKinnel
