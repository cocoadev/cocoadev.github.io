---
layout: page
title: UniformTypeIdentifier
---

The General/UniformTypeIdentifier (UTI) was introduced in Mac OS X 10.3 and are documented in General/LaunchServices/UTT<nowiki/>ype.h.

A UTI looks suspiciously like a bundle identifier. It is used by General/LaunchServices and General/PasteboardManager to identify the types of files and data, and is passed around in General/NSString/General/CFString format.

As of Mac OS X 10.4, the General/UniformTypeIdentifier is the preferred way of identifying file types and, indeed, the types of data and other entities. The General/UniformTypeIdentifier is documented here:

http://developer.apple.com/documentation/Carbon/Conceptual/understanding_utis/

Public and Apple General/UTIs are listed here:

http://developer.apple.com/documentation/Carbon/Conceptual/understanding_utis/utilist/chapter_4_section_1.html

and here:

http://developer.apple.com/qa/qa2005/qa1406.html

and, in 10.4, General/LaunchServices/UTC<nowiki/>oreTypes.h.

----

I think it's very unfortunate that Apple created their own metadata/type system. If they had based it upon RDF, it would be much more interoperable. Inverse-DNS identifiers (bundle General/IDs, General/UTIs, etc.) should go away and be replaced with standardized URI formats, such as the General/TagUri (which will soon be enshrined as an RFC). RDF classes could even replace MIME types (in a future official revision of the format, of course), so we don't have the problem of needing to either register them with the IETF or prefix them with X-. Apple's just reinventing the wheel, which is bad for standardization and interoperability. I'm not saying that the W3C and the IETF should become all-powerful oligarchies in terms of Internet-standardization power, but the fact is, they've standardized a good metadata and type system which is already in wide use on the Web. Apple should have integrated the RDF data model with Spotlight instead of coming up with their own data model.

(Disclaimer: I'm talking about the RDF *data model* -- that is, sets of subject-predicate-object triples consisting of General/URIs or literal strings. Not the depraved XML format. The last thing we need is a bunch of XML crap in filesystem drivers.)

-General/AdamAtlas

----

It would be really nice to have some big tree of a lot of standard UTI types.  Does anyone know if such a thing exists?

----

The information you're looking for exists on your system, just not in a very readable format.  Lucky for us someone else has done that work:
http://toxicsoftware.com/blog/uttypes_graph

Adam: I notice you haven't pointed out any reasons why General/UTIs are no better than these existing standards. (Other than them not being standards yet...)  I can think of one reason they are: low signal/noise ratio.

public.text

Now that's succinct. :-)

-Harvey
