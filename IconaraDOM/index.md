---
layout: page
title: IconaraDOM
---



Iconara DOM framework is a Cocoa-framework for accessing, manipulating and outputting XML-data. It's similar to the JDOM and XOM frameworks for Java. http://goo.gl/OeSCu

http://iconaradom.sf.net/

The current version is v2.0.

What it's good for:

*Parsing an XML file, getting a specific part, change it and write the file back again
*Building a document in memory, writing it out as a file
*Converting from XML to an internal representation or vice versa
 

Features:

*Namespace support.
*High level document builder API, can support any XML parser. Currently ships with Expat as default builder, and an experimental version of a libxml2 builder.
*Customizable document formatter that pretty-prints XML.
*XPath support (so far basic, handles all paths, but limited predicate support).
*Public interface is not tied in with a specific implementation, which means that you can write your own that will interact seamlessly with the default.


Why choose IconaraDOM over NSXML:

**Pre-tiger:** First of all, NSXML on 10.3 is an XML parser, IconaraDOM is so much more than that. The document object model, which IconaraDOM complies to, is a standardized way of manipulating data that has been parsed from XML, or is going to be serialized into XML. NSXML is an event-driven parser, wich is fine for many applications, but not very good if you want to have random access to the document, to change things around, query the document for it's structure, and so on. That is exactly what the DOM is about: providing a tree-representation of an XML document, that can be edited and then written back to file. Many times you would find yourself creating an internal representation of the events that you get from NSXML, sort of a home-made DOM structure. IconaraDOM would simplify that task for you, providing the internal representation, and much more.

**Tiger:** In 10.4 NSXML has been extended to support a simple tree based representation of an XML document. I belive it's built on     libxml2. Apple says it's modeled on XQuery, rather than the DOM, which I feel is just a way to make excuses for not implementing a full DOM. Anyhow, NSXML seems quite competent and for most purposes I recommend using NSXML instead of IconaraDOM. If you need more, for example configurable output, complete DOM support, more complete XPath support (I might be wrong on this, but     libxml2 is lacking in it's support somewhat) as well as configurable parsing and other things, IconaraDOM might be the better choice. I will continue to work on the framework and build converters to and from NSXML, and hopefully I can make IconaraDOM work like a superset of NSXML, giving you the best of both worlds. And, of course, IconaraDOM works on 10.2, possibly even 10.1, so if you don't write exclusively for Tiger, NSXML is not an option.


The framework i GPL with an exception for open source applications, but I am open to alternative licencing if there's a good reason for it, there's a discussion further down about this.

Also see: http://www.w3.org/DOM/, http://www.jdom.org, http://www.xom.nu, XMLTree, WritingXMLFiles

----

Some example code:

This snipplet parses the contents of the SoftwareUpdate preferences on your system and finds the date when you last updated your system.

    
// build a new document from the SoftwareUpdate preferences file
id <DOMDocument> doc = [DOMBuilder 
	documentFromFile:@"/Library/Preferences/com.apple.SoftwareUpdate.plist"
];

// get all key-elements (<key>text</key>)
NSEnumerator *keys = doc childElementsByName:@"key"] objectEnumerator];

id <[[DOMElement> key = nil;	
id <DOMElement> lastDate = nil;
	
while ( key = [keys nextObject] ) {
	// for each key, check if the text content is "LastSuccessfulDate"
	if ( key textContent] isEqualToString:@"[[LastSuccessfulDate"] ) {
		// if found, save the next element in document order
		lastDate = [key nextSibling];
	}
}

if ( lastDate != nil ) {
	// print the text contained in the element
	NSLog( [lastDate textContent] );
} else {
	NSLog( @"You have never updated your system" );
}


There is a short FAQ (http://developer.iconara.net/products/DOM/docs/FAQ/index.html) with some more examples that can get you started. There is also an example application that uses the framework to do pretty-printing, XPath searches and RSS parsing at http://developer.iconara.net/products/DOM.

----

**Discussion, more examples and questions**

*Discussions about old versions of the framework have been removed, the bugs have been fixed and the examples are no longer valid. Please post new questions or comments here, or mail me at iconara at users dot sourceforge dot net, I answer all questions about the framework, providing examples and suggestions, within a day or two, depending on how much time I have. Don't hesitate mail your questions, you will get an answer, and I will most likely solve your problem.*

-- TheoHultberg/Iconara

----

**Questions on the licencing scheme**

Any chance of changing the license to Lesser GPL or something else so I can use the library in my commercial program?

*No. But if you want to use the framework in your commercial program, please contact me and describe you licensing scheme. I am not unwilling to release specially-licensed versions of the framework (� la MySQL). The rationale behind GPL in this case is that I want to release the source, but at the same time I'm not interested in anyone using it for commercial programs because of all the work I have put into it. It's free as in beer if you give something back to the community, it's not if you don't. So if it's payware we're talking about, I want my share. If it's just closed source, I want a good reason for not releasing it as OpenSource.* --TheoHultberg/Iconara

*The correct question would have been 'if I pay you royalty, can I use your framework in my commercial program'. Then the answer would have been yes.* --Theo

----

Well then there is no benefits of it over libxml2 (http://xmlsoft.org/) which is also included with OS X, using the LGPL makes sure your framework remains open source but allows everyone to use it, I will not support a framework/library that supports the viral nature of the GPL.

*Being ObjectiveC is a great benefit IconaraDOM has over libxml2. I have struggled with libxml2 (and written a ObjectiveC wrapper for it, which makes it compatible with the IconaraDOM framework), it is not an easy API to work with, it's pseudo-OO C and the documentation is horrendous. It's not DOM compliant and it lacks many of the features of IconaraDOM. It's got some nice stuff, but it's not easy to use. I would be very surprised if libxml2 will gain any significant userbase in the Cocoa community. The point with IconaraDOM is that it is for Cocoa. That is the greatest benefit.*

*I can see that you don't agree with the GPL model, and that is fine by me. I, however do, and because of that I have chosen this licencing scheme. But I also stress again: I'm open to suggestions, I would consider alternative/commercial/exclusive licences, but there has to be a reason. If you want to make shareware or a commercial application with my code, please do, but I want my share. If you want to create freeware but closed-source, I want a good reason, a very good reason for it not being OpenSource. I chose not to use LGPL exactly because it allows everyone to use it, why should you benefit from my hard work? To me there are two alternatives: payware, or GPL.* 

*It's not that I'm asking anyone to use my framework, more the other way around. I provide a way for you to write less code, to simplify your task. If you don't agree to my terms, fine, but you'll have to do without. I'm not trying to be rude, just point out how it is. You can't expect to get everything for free. The guys releasing their software with BSD, LGPL or Apache -style licences are really nice people, they put in a lot of effort to provide something totally for free, but don't expect everyone to be like that.*

--TheoHultberg/Iconara

Indeed you have the perfect right to choose this licensing model if you choose to do so, however know this, if you do use this don't expect much use of your product beyond products such as Adium or other open source products, there are many other solutions and people will use them.

I think I will just go out and write an objC wrapper for libxml2 so anyone could use it and be able to use non restricted code in their application.

*Actually, I have been doing some thinking the last few days. When it comes to my decision to use the GPL for licencing the framework, it's the implementation I'm concerned with. In v2.0 the implementation and the public API has split, and the intention is that alternative implementations should be able to co-exist with my implementation, and my     libxml2 version was a proof of concept for that. You say that you want to write an ObjC wrapper for     libxml2 that is not restricted, and I believe that to be a GoodThing. However, your implementation would most likely have a different public API to the IconaraDOM framework, and they would be incompatible. If you actually write your     libxml2 wrapper, I would like it to adhere to the public API of my framework, and I would have no problem with making the protocols that make up the public API LGPL or similar to make that possible. My concrete implementation, along with XPath, traversal, formatter, Expat builder and so on would still be GPL, though. --Theo*

----

By demand from the Growl ( http://www.growl.info ) project, I have included a MySQL-style exception to the GPL, allowing for any project that releases it's source under any licence approved by the Open Source Initiative to use the DOM framework without experiencing the viral effects of the GPL. Growl is BSD licenced, and will remain so, even if it uses the IconaraDOM framework. --TheoHultberg/Iconara

