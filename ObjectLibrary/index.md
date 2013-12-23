---
layout: page
title: ObjectLibrary
---



This page is for tracking re-usable Cocoa classes that can be mixed, matched, and dropped fairly easily into existing Cocoa projects to add useful functionality.

Full application source code is not quite what I'm looking for here, but rather individual packaged objects that perform a specific task without having too many external dependencies.

----
See also CocoaSampleCode, CocoaDevUsersAdditions.

Parts of the object library have been split off into subpages:

[Topic]
----
**RECENT CHANGELOG**

* 23 June 2012 - Added ESSVideoShare (under Kits)
* 4 June 2011 - Added HighlighterKit and SourceCodeKit
* 24 July 2009 - Added LIFlipEffect
* 4 June 2009 - Added SQMaskImageRep
* 17 May 2009 - Updated URL for Cocoa XML-RPC Framework
* 24 April 2009 - Added CoreSearch
* 11 March 2009 - Added PostgresKit
* 16 February 2009 - Added DrawKit
* 5 November 2008 - Added SCEvents
* 14 November 2007 - Added ZipArchive
* 20 September 2007 - Added ActiveRecord
* 8 September 2007 - Added DPQueue, DPUIElement and DPRunLoopSource
* 4 September 2007 - Added CSRegex and CSCoroutine
* 3 September 2007 - Added BaseTen
* 3 September 2007 - Added RegexKit
* 22 July 2007 - Added GDFramework
* 6 July 2007 - Added LinkScraper and BatchDownloader
* 6 January 2007 - Split out ObjectLibraryViews and ObjectLibraryNetwork, because ObjectLibrary is now too big to add to. See ObjectLibraryRefactoring.
* 13 December 2006 - Added LRFilterBar and LRCalendarView
* 9 November 2006 - Added StoppableThreads
* 18 October 2006 - Added GCGradient
* 17 October 2006 - Added GCZoomView, MPSearchQuery
* 30 September 2006 - Added Bonzo Framework
* 16 September 2006 - Added SGUNArray
* 7 September 2006 - Added CompressorAPIFramework
* 31 August 2006 - Added GTResourceFork
* 13 May 2006 - Added IFVerticallyExpandingTextField
* 7 May 2006 - Added MailCore
* 24 April 2006 - Added SGUNumericalCocoa
* 2 April 2006 - Added JKPTree

----

**Networking**

See ObjectLibraryNetwork.

----

**Threads**

***InterThreadMessaging** - http://files.me.com/d_j_v/3trcvx - Perform selectors and send notifications in the context of other threads. An alternative to DO.

***NDRunLoopMessenger** - http://homepage.mac.com/nathan_day/ - Provides light weight version of DO, can send messages with the API NDRunLoopMessenger or get a NSProxy object from NDRunLoopMessenger and use that instead.

***ThreadMessage** - http://blackholemedia.com/code/ - Simple alternative to using DistributedObjects for communication between threads

***ThreadWorker** - http://iharder.sourceforge.net/macosx/threadworker - One-shot worker-thread class

***ProtoThreads** - http://www.frameworklabs.de/protothreads.html - Light weight, cooperative threads to simplify programming against asynchronous APIs.



----
**Controls & View Classes**

See ObjectLibraryViews.



----
**Database**

***DataCrux** - http://treehouseideas.com/datacrux/ - A high-level, object-relational framework that provides an embedded SQL database engine for use in applications. Based on SQLite (and Blackhole Media's SQLDatabase), so no server process runs on the machine.  The public API is very simple, and there's no need to write query strings. Developer defines object-db mapping in a property list, and the framework does the rest. Works with Jaguar and Panther. Open source BSD-style license.

***iODBC** - http://www.iodbc.org/ - Complete cross-platform implementation of an ODBC v3.52 Driver Manager.  Open Source SDK enables building of ODBC-compliant Client Drivers and Client Applications.  SDKs for Mac OS 9 and Mac OS X, linkable as dylibs or Frameworks, support Cocoa, CFM and Mach-O Carbon, and Classic builds.  Full Unicode support, 64-bit ready.  Dual-licensed under LGPL and BSD License.  iODBC v3.52.1 SDK current as of January 2005; iODBC 3.0.6 dylibs bundled into Mac OS X 10.2.x-10.3.x and Darwin 6.x-7.x.  Free Web-based up-and-running and development support from maintainers, OpenLink Software, http://support.openlinksw.com/

***MacSQL Framework** - http://www.rtlabs.com/fwork/ - Framework for adding support for MySQL, PostgreSQL, Oracle, Sybase, MS SQL Server, OpenBase, and FrontBase to Cocoa and AppleScript Studio applications. Includes Interface Builder palette with widgets to query/update the database without writing any code. (not free)

***QuickLite** - http://www.webbo.com/ - Interface to SQLite. A much more complete package than the other SQLite database libraries. Also, check out the included SQLManagerX for database access.

***SQLDatabase** - http://blackholemedia.com/code/ - Obj-C interface to the SQLite embeddable SQL database library

***SQLLite Browser** - http://sqlitebrowser.sourceforge.net - Interface to SQLite

***BaseTen** - http://www.karppinen.fi/baseten - Nice, CoreData like framework for working with PostgreSQL

***ActiveRecord** - An Active Record implementation in objective-c

***PostgresKit** - http://code.google.com/p/postgres-kit/ - Embed PostgreSQL server in your Cocoa apps, etc.



----

**XML**


***Cocoa XML-RPC Framework** - http://github.com/eczarny/xmlrpc/tree/master/ - A lightweight XML-RPC client framework for Cocoa.

***SAXy OX** - https://github.com/reaster/saxy - Object-to-XML Marshalling Library. Features include: * Efficient reading/unmarshalling and writing/marshalling of XML from domain objects. * Full XML namespace support. * Built-in type conversion and formatting. * Highly configurable API. *Self-reflective automatic mapping. * Optimized for iOS with no no third-party dependencies.

***Iconara DOM Framework** - http://iconaradom.sf.net/ - Framework for working with XML-data. Conforms to the DOM Level 3 Core specification as far as it's useful in ObjC. Also includes Objective-C frontends to the Expat parser, a plug-in architecture for parsers, basic XPath support and output formatters -- see IconaraDOM

***SKYRiX XML Processing Libraries** - http://www.opengroupware.org/en/devs/sope/skyrix_xml/ - a set of three frameworks for doing XML processing using Objective-C. Contains a SAX, DOM and XML-RPC implementation.

***UKXMLPersistence** - http://zathras.de/programming/sourcecode.htm#UKXMLPersistence - A category on NSDictionary that allows converting between XML and dictionaries. The tags are the keys, the values are the data. Makes for very readable XML and just as easy as property lists. The core is written with CoreFoundation, allowing for use in Carbon apps as well.

***XMLTree** - XMLTree is a Public Domain Objective-C wrapper for Apple's CFXMLParser which is provided in the CoreFoundation.

***Excelsior!** - http://homepage.mac.com/jimbokun/Excelsior.html (BROKEN LINK) - XML marshaller for Cocoa/Objective C. Generate Objective C object instances from XML data or XML data from Objective C instances with just a few messages. An external file defines how the marshalling takes place.

----
**Data Types**


***BDAlias** - http://eschatologist.net/bDistributed.com/ - A class for dealing with alias records, a form of persistent file reference that is more robust and provides a better user experience than a textual path.  Open Source with a BSD license. **Link seems unresponsive.**

***GTResourceFork** - http://www.ghosttiger.com/?p=117 or http://code.google.com/p/macfuse/source/browse/external/filesystems-objc/GTResourceFork?r=328 - A thread-safe class for reading and writing resource forks.

***IconFamily** - Interface with carbon to set/get finder icons, plus so much more

***JTAddressBook** - (broken link) http://www.jtechsoftworks.com/samples/JTAddressBook.sit - Categories added to ABMultiValue, ABRecord and ABPerson to ease the development of code that works with the Address Book.

***KTMatrix** - Collection class bridging the gap between NSArray and NSDictionary. Currently beta. See the DesignMatrix page for more.

***MTCoreAudio** - http://aldebaran.armory.com/~zenomt/macosx/MTCoreAudio/ - a Cocoa-flavored Objective-C wrapping around the Hardware Abstraction Layer (HAL) of Apple's CoreAudio library.

***NDAlias** - http://homepage.mac.com/nathan_day/ - A class to represent and alias record in Objective-C, can also be used for creating and modifying alias files.

***NDResourceFork** - http://homepage.mac.com/nathan_day/ - A class for reading and writting Resource Forks.

***NDProcess** - http://homepage.mac.com/nathan_day/ - A wrapper class for Apples Process Manager Functions.

***QSW_UUID** - http://www.quecheesoftware.com/downloads/UUID.html - class wrapping Core Foundation Universally Unique IDentifiers API (modified Creative Commons Attribution license)

***SnoizeMIDI** - A framework containing code for dealing with CoreMIDI in a Cocoa app.

***week** - http://www.oops.se/~malte/software.html - Categories to NSCalendarDate to work with ISO 8610 dates.

***JKPTree** - A simple Cocoa wrapper for the CF type CFTree.

***SGUNArray** - http://www.angelfire.com/planet/syhenry/ - A high performance class cluster that wraps C arrays.

***DPQueue** - http://www.dpompa.com/CodeSnippets - A thread-safe two-lock queue, implementing the Michael & Scott algorithm.

***DPRunLoopSource** - http://www.dpompa.com/CodeSnippets - An Objective-C wrapper around the CoreFoundation CFRunLoopSource API. It is implemented as an abstract class you subclass in order to create your own runloop source.


----

**Scripting**

***Direct Dispatching** - http://files.me.com/d_j_v/pm3snz - A category to help apps handle Apple Events they send to themselves for recording purposes.

***FScript** http://www.fscript.org - An embeddable, open-source scripting engine for Cocoa.

***KFAppleScript Handler Additions** - http://homepage.mac.com/kenferry/software.html - Call functions in compiled applescripts, with arguments, and get return values.

***NDAppleScriptObject** - http://homepage.mac.com/nathan_day/ - A class to represent an AppleScript, provides additional functionality to NSAppleScript.

***LuaCore** - http://gusmueller.com/lua/ - An Objective-C framework for running Lua scripts. It includes a bridge so that you can pass in Objective-C objects, and work with them in the Lua environment. 


----

**"Kits"**
Largish collections of classes designed to be used together.


***OmniFrameworks** - Contains OmniAppKit, OmniFoundation, OmniBase, and others.  Used by OmniGroup in their apps. Very broad range of functionality, somewhat lacking in documentation.

***AGKit** - http://sourceforge.net/projects/agkit (new) http://students.washington.edu/grnmn/ (old) - Contains AGRegex, AGSocket, and AGProcess. AGRegex: Perl-compatible pattern matching to Cocoa applications using the PCRE regular expression library, AGSocket: A BSD sockets framework for use in Cocoa applications, AGProcess: A class for getting Unix process statistics

***Bonzo Framework** - http://weblog.bignerdranch.com/Bonzo/ - Submission-friendly collection moderated by Aaron Hillegass.  MiscKit 2.0?

***DrawKit** - http://apptree.net/drawkit.htm - comprehensive graphics system for doing interactive vector drawing. Highly customisable, easy to get working "out of the box" and adapts to many common requirements easily. Could use some manpower to help bring documentation, etc up to speed. (was previously named GCDrawKit).

***EDFrameworks** http://www.mulle-kybernetik.com/ - 
One framework that provides seamless extensions of Foundation and AppKit. Two more frameworks that provide services for Internet applications, style sheet driven export of your objects and message handling. All with a BSD style OpenSource license. 

***HighlighterKit** - http://svn.gna.org/viewcvs/pmanager/HighlighterKit/ - A framework for SyntaxHighlighting in code editors.

***IDEKit** - http://projects.gandreas.com/idekit/ - A framework that provides "source text views" - programmer friendly editors that include syntax coloring (with plugin support for different languages), function pop-ups, split views, and (in the next version) folding.

***MiscKit** - http://www.misckit.com/ - coming soon, the MiscKit! **--Really? It seems to me like it's dead and has been for awhile. Where are you MiscKit?**

***MOKit** - http://mokit.sourceforge.net/ - Includes a cocoa-wrapped implementation of Henry Spencer's Regular Expression library as well as a number of other interesting tidbits

***MPWFoundation** - http://www.metaobject.com/Technology.html - MarcelWeiher's HigherOrderMessaging implementation as well as other miscellaneous stuff

***RegexKit** - http://regexkit.sourceforge.net/ - PCRE based regular expression extensions to NSArray, NSDictionary, NSSet, and especially NSString.

***SourceCodeKit** - http://svn.gna.org/viewcvs/etoile/trunk/Etoile/Languages/SourceCodeKit/ - A framework for building IDEs (cf. IDEKit) based on libclang.

***ESSVideoShare** - https://github.com/eternalstorms/ESSVideoShare-for-OS-X-Lion - A framework for OS X and iOS to easily implement uploading videos to various sharing sites



----

**Misc / Unclassified**


***STSTemplateEngine** - http://www.sunrisetel.net/software/devtools/STSTemplateEngine.shtml - A Universal Template Engine in Objective-C/Cocoa for embedding in Cocoa applications. Includes a macro language for conditional template expansion and supports unicode.   Open Source under GPL license. Other licensing options upon request.

***AMShellWrapper** - http://www.harmless.de/cocoa.html - This class is based on TaskWrapper from Apple's Moriarity sample code. Use it to run commandline tools from your application. Connect your own methods to stdout and stderr, get notified on process termination and more.

***BDControl** -http://eschatologist.net/bDistributed.com/ - A framework containing qualifiers and sort orderings similar to those in the old EOControl framework.  Open Source with a BSD license.

***BDRuleEngine** - http://eschatologist.net/bDistributed.com/ - A framework for building rule-driven applications, similar to the DirectToWeb rule engine in WebObjects.  (Requires BDControl.)  Open Source with a BSD license.

***Narrative** - http://sourceforge.net/projects/narrative/ - A Cocoa/GNUStep native plotting framework for Mac OS X. (LGPL)

***NDLaunchServices** - http://homepage.mac.com/nathan_day/ - A wrapper class for LaunchServices.

***OgreKit** - http://www8.ocn.ne.jp/~sonoisa/OgreKit/ (Translated by Google[http://translate.google.com/translate?u=http%3A%2F%2Fwww8.ocn.ne.jp%2F%7Esonoisa%2FOgreKit%2F&langpair=ja%7Cen&hl=ja&c2coff=1&ie=UTF-8&oe=UTF-8&prev=%2Flanguage_tools]) - Full-featured regex framework. Documentation is in Japanese, but it's not too complicated to use.

***ObjPCRE** - http://sourceforge.net/projects/objpcre
- NSString oriented regular expressions framework.
- Well documented.
- Simple to implement and utilize.

***OPML** - http://ranchero.com/cocoa/opml/ - Class for parsing OPML files. (BSD License)

***UKDocumentationController** - http://www.zathras.de/programming/sourcecode.htm#UKDocumentationController - A tiny controller class that you hook up to some items in your "Help" menu to call up "Readme", "Revision History" etc. anchors in your help book directly.

***UKFontMenuController** - http://www.zathras.de/programming/sourcecode.htm#UKFontMenuController - A controller class that gives you a Carbon-style font menu that lists all font collections (or one particular one) and allows simply picking a font from the menu to apply it to the current field's text.

***UKIdleTimer** - http://www.zathras.de/programming/sourcecode.htm#UKIdleTimer - A wrapper around Carbon's EventLoopIdleTimer. This is a timer that fires whenever the user has been idle for a certain amount of time.

***UKKQueue** - http://www.zathras.de/programming/sourcecode.htm#UKKQueue - A wrapper class around the     kqueue(2) file change notification mechanism.

***MyBlocks** - http://dwt.de.vu/ - Classs for using Smalltalk Style Blocks with Obj-C (as far as possible with C)

***GradientTest** - http://toxicsoftware.com/blog/index.php/weblog/gradienttest/ - Class for using CoreGraphics to draw smooth gradients in NSViews.

***PDPowerManagement** - http://developer.phildow.net/cocoa-code/pdpowermanagement/ - Class for registering for sleep notifications and preventing idle sleep.

***SGUNumericalCocoa** - http://www.angelfire.com/planet/syhenry/ - Several classes: extension to NSNumber for complex data types, classes for continuous ranges and sets of ranges. 

***MABSupportFolder** - http://developer.mabwebdesign.com/getfile.php?file=46 Class to easily manage a app support folder

***CompressorAPIFramework** - http://mutablelogic.com/cocoa/CompressorAPIFramework.zip Cocoa framework for programmatically calling Apple's Compressor (part of Final Cut Studio) and monitoring compression jobs.

***StoppableThreads** - An NSThread replacement that allows threads to be stopped and joined.

***LinkScraper** - http://konstochvanligasaker.se/code - LinkScraper is a Cocoa class that "scrapes" (gives you a list of all links) for a given URL.

***BatchDownloader** - http://konstochvanligasaker.se/code - BatchDownloader is a Cocoa class that downloads a bunch of URLs asynchronously, and notifies you when it's done.

***SingleBlockingDownload** - http://konstochvanligasaker.se/code - SingleBlockingDownload is the exact opposite of BatchDownloader. It downloads any file synchronously (blocking), returning the downloaded data to you once it is done.

***GDFramework** - http://code.google.com/p/gd-kit/ - Interface to the GD library for image reading, writing, resizing, cropping, drawing and filtering.

***CSRegex** - An extremely lightweight regex class, using POSIX regexes instead of Perl-compatible ones.

***CSCoroutine** - CoRoutine**'s for Cocoa.

***DPUIElement** - http://www.dpompa.com/CodeSnippets - A set of wrappers around the carbon accessibility API plus some useful extensions.

***ZipArchive** - http://code.google.com/p/zip-framework - A framework / class for reading from zip archives

***SCEvents** - http://stuconnolly.com/projects/source-code/ - An FSEvents Objective-C wrapper

***CoreSearch** - http://github.com/simonmenke/coresearch/tree/master - An Objective-C wrapper for SearchKit

***SQMaskImageRep** - http://www.lorem.ca/samplecode/SQMaskImageRep.tar.bz2 - Clip Cocoa drawing to irregular, or semi-transparent masks

***LIFlipEffect** - http://www.lorem.ca/samplecode/LIFlipEffect.zip - Core Animation-based window flipping code.

