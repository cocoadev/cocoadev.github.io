---
layout: page
title: ObjCAppscript
---

objc-appscript is an easy-to-use, high level ObjC-to-Apple event bridge for OS X 10.3 and later. 

See: http://appscript.sourceforge.net/objc-appscript/index.html

objc-appscript aims to provide:


* the same level of features and functionality as provided by AppleScript along with several additional benefits such as thread safety and support,
* a rich, flexible API suitable for controlling Apple event-aware applications with or without AppleScript dictionaries, and
* a level of real-world application compatibility comparable to AppleScript's.


objc-appscript is based on the well regarded and very mature Python appscript bridge, and while it is still under development it is already very usable.

The bundled 'osaglue' tool can be used to convert an application's AppleScript dictionary into human-readable ObjC 'glue' classes, e.g.: to generate glue files for TextEdit using the classname prefix 'TE':

     
osaglue -o TEGlue -p TE /Applications/TextEdit.app
 

These glue files can be imported and used to control TextEdit directly from ObjC, for example:

     
/*
 * tell application "TextEdit"
 *     get text of document 1
 * end tell
 */

TEApplication *textedit = [[TEApplication alloc]  initWithBundleID: @"com.apple.textedit"];
TEReference *ref        = [textedit documents] at: 1] text];
[[NSString *result        = ref get] send];
[[NSLog(@"Result: %@", result);
[textedit release];


objc-appscript also provides several classes very useful to developers working with NSAppleScript; in particular the AEMCodecs class which provides automatic translation of values between a range of common Apple event types and their Cocoa equivalents.

-- hhas

----

What do -documents and -at: return?  

The mental model required for working with these trampolines is, to say the least, confusing.  This does not mesh well with what a Cocoa.

----
A TEReference instance, which is a thin wrapper around an instance of an AEMSpecifier subclass, which packs into an AEDesc of (in most cases) typeObjectSpecifier when you send an Apple event. 

To export application dictionaries in objc-appscript format, use ASDictionary (http://appscript.sourceforge.net/download.html).

No, the way appscript works isn't particularly Cocoa-like, but despite common misconceptions Apple event IPC is *not* object-oriented a-la COM/CORBA/DO - it's *remote procedure calls plus simple first-class relational queries*. Think 'XPath over XML-RPC' as a rough analogy.

Here's how Apple event IPC works (over-simplified a bit for clarity):

1. You build up a query ('reference' in AppleScript parlance), then send it to the target application in an Apple event ('command') along with any other data that's needed (which may be simple values and/or additional queries). 

2. The application resolves that query against its AppleEventObjectModel, then performs the command on the object or objects identified.

3. Finally, the application returns either a result or error (where appropriate). The result may be a simple value (string, integer, date, etc.), another query, or a collection (list or record) of other values.

Obviously, you're going to get a bit of impedance mismatch when bridging a RPC+query driven system with an object-oriented system. Witness the challenges faced by relational database ORMs, for example; Apple event bridges don't have it quite so bad since at least the AppleEventObjectModel is tree-shaped rather than multiple-table-shaped, but there's always going to be a certain degree of awkwardness where they join (more on this below).

Appscript, like AppleScript, respects Apple event semantics. Appscript is also more transparent than AppleScript as it avoids most of the magic favoured by the latter such as implicit 'gets' and those awkward considering/ignoring/timeout blocks. Really, its only concession to 'magic' is a bit of syntactic sugar to provide users with a nice terminology-based syntax instead of the cryptic four-character-codes used by the AppleEventManager. For example:

    
TEApplication *textedit = [[TEApplication alloc] initWithBundleID: @"com.apple.textedit"];

TEReference *ref = [textedit documents] at: 1] text];

[[TEGetCommand *cmd = [ref get];

NSString *result = [cmd send];


is merely human-readable shorthand for:

    
AEMApplication *textedit [[AEMApplication alloc] initWithBundleID: @"com.apple.textedit"];

id query = [[[AEMApp elements: 'docu'] at: 1] property: 'ctxt'];

AEMEvent *event = [textedit eventWithEventClass: 'core' eventID: 'getd'];

[event setParameter: query forKeyword: keyDirectObject];

NSString *result = [event send];


(As for the equivalent solution using NSAppleEventDescriptor and AESendMessage only, that's a couple dozen lines of really tedious code which I'm not going to write right now.)

If you try to think about appscript/AppleScript/Apple events in object-oriented terms, they won't make an ounce of sense beyond the most superficial of 'understandings'. Approach them on their own terms, however, and it's actually a pretty elegant, if unusual, system ideally suited to high-latency communication. As a simple mantra, just think: "Build the query. Send the query. Get more queries back."

----

----

If you don't like it, you might like Apple's 10.5 ScriptingBridge, which uses a similar system but with more Cocoa-ish semantics (eg element collections are NSMutableArray and -makeObjectsPerformSelector: is optimized as a single Apple Event under the scenes) --EmanueleVulcano aka millenomi

----

The problem with ScriptingBridge is that it provides these 'Cocoa-ish' semantics basically by fibbing its face off about how Apple event IPC actually works (see above). The result is something of a house of cards built of user misconceptions, brittle behaviours, and impenetrable obfuscations. Like AppleScript, it works acceptably up to the point that its faux-OO abstractions start leaking... and then the user is pretty much hosed because they've no idea what's going on behind all that smoke and mirrors and sleight of hand.

To take one example: NSMutableArray-based element collections are, like cake, a lie. The AppleEventObjectModel is based on object attributes and one-to-one and one-to-many relationships between objects. To pretend that one-to-many relationships are VBA-style Range objects, they had to fudge a whole lot of details. I'm still working on a list of all the ways this can possibly go wrong, but one of the simpler ones that has already hit several users is the problem where -addObject: fails for some applications. e.g. See this recent thread on AppleScript-implementors:

http://lists.apple.com/archives/Applescript-implementors/2007/Nov/threads.html#00034

And then there's other problems, such as the clumsy, inferior NSPredicate-based substitute for the powerful by-filter ('whose' clause) reference form, completely crap enumerator support, presence of thread-unsafe state, intolerably bad error reporting mechanism, ugly and error-prone Python syntax, lack of support tools, and so on. Some of the more trivial defects (e.g. enumerators, support tools) may eventually get fixed in future OS releases, but others are baked hard into ScriptingBridge's fundamental design such that they can't be fixed short of throwing the whole thing away and starting over with a brand new design.

...

Personally I find it rather ironic that after years of professional programmers complaining about AppleScript being opaque, cryptic, hard to understand, and so on, Apple have actually managed to come up with something that's even more opaque, cryptic, hard to understand, etc. Though whether this is down to ignorance, arrogance, or a misguided desire to protect gentle object-oriented Cocoa users from the unfamiliar and sometimes harsh and undisciplined realities of RPC+relational query-based Apple event IPC, I dunno; only Apple could tell you that.

However, it is disappointing that after 15 years of waiting for Apple to provide high-level Apple event bridging to other languages, they best solution they could come up with is decidedly inferior to AppleScript, not to mention some of the third-party bridges as well (UserTalk, PythonAppscript). Hence the ongoing port of Python appscript to ObjC in order to provide more demanding Cocoa developers (including myself) with a more powerful, flexible, compatible alternative. Appscript doesn't nanny its users - it expects them to have some basic understanding of how Apple event IPC actually works (the appscript documentation includes background information and a tutorial for newcomers, btw), and accept that the many quirks, flaws and inconsistencies of the applications they want to control are beyond the abilities of a mere bridge to fix so are something they will have to learn about and deal with for themselves. In return, it doesn't lie to them or dictate what they can or can't do, and it provides them the means to deal with even the crankiest and most obtuse of 'AppleScriptable' applications in decent comfort.

Anyway, if someone'd like to kick off a couple of ScriptingBridge and ScriptingBridgeCriticisms pages, that's probably the best place to continue this sort of discussion.

-- hhas

