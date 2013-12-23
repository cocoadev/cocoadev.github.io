---
layout: page
title: CoreFoundation
---


<span class="plainlinks">[http://www.propertykita.com/rumah.html<span style="color:black;font-weight:normal; text-decoration:none!important; background:none!important; text-decoration:none;">Rumah Dijual]</span>
CoreFoundation is an API, which is part of the CoreServices which sits underneath both Carbon and Cocoa, and above the Core OS (MachMicroKernel/BSD).

It provides some basic "data collection" types, such as trees and dictionaries. These are available as CFTypeRefs.

It also offers some utilities such as XML parsing, and application preferences management, areas of functionality which either aren't available at all in Foundation or are less complete.

Most of Foundation is implemented using CoreFoundation. The API is C Language rather than ObjC, but concepts of ObjectOrientedDesign prevail.
Documentation is at http://developer.apple.com/documentation/CoreFoundation/CoreFoundation.html
CoreFoundation has lots of CFTypeRefs. Many other APIs use CFTypeRefs, such as SystemConfiguration and some of CarbonFramework.
[http://www.pintuvariasi.com Pintu Aluminium]
Headers for CoreFoundation can be found at file:///System/Library/Frameworks/CoreFoundation.framework/Headers/ , inside the easy-to-include Framework.  I also recommend looking at CoreServices and ApplicationServices.

CoreFoundation has many functions that simply aren't implemented in Foundation.  Some are presented here:

[http://www.mitrainti.com SAP Indonesia] 

* CFXMLParser: Customized XML parsing. (Foundation has NSXMLParser, but they aren't toll-free bridged. *--boredzo*)
* CFNetwork: Customized HTTP Requests (in CoreServices). (Foundation can do raw network traffic with NSSocketPort. *--boredzo*)
* CFStream: Streams for files, memory, and even TCP sockets (similar to SmallSockets' BufferedSocket). (Foundation has NSFileHandle, but only for files, not memory or sockets. *--boredzo*)
* CFBitVector: A more expedient way of accessing individual bits than C's bitwise operators.
* CFTree: A collection type. Each instance is a node in the tree.
* CFUserNotification: For background/daemon processes to display an Aqua-style modal dialog to query the user.
* CFURL FSRef support: easily convert to and from FSRefs for legacy Carbon APIs, lacking in NSURL.



CoreFoundation also has the advantage of being callable from Carbon and Cocoa code.

Note: Most of the CoreFoundation types that have Foundation equivalents (with the same name, e.g. NSString and CFString) use TollFreeBridging. This means that one can pass an NSString * to a function expecting a CFStringRef and vice versa. The same goes for arrays, dictionaries, URLs, and others in which case a simple typecast will suppress the compiler warning:

someNSStringVariable = (NSString *)someCFStringVariable;

or

someCFStringVariable = (CFStringRef)someNSStringVariable;

