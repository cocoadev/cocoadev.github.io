---
layout: page
title: WorkingWithUserDefaults
---


== Working With User Defaults ==

Mac OS X introduces a unified way applications can store preference data. Applications no longer need to (although poorly written ones will) roll their own preference file handler. This system is sometimes called a Preferences system and is sometimes called User Defaults. I will use these terms interchangeably here.

The User Defaults system has three parts to it:


* API allowing programs to access user preference data.
* The physical storage for the preference data.
* A handy tool that allows the user to manipulate the preference data.


Preferences are organized via three main categories. These categories are basically determined by their physical storage, but since the categories affect the API, storage, and tool use equally, they bear mention here:


* Domain - A preference/defaults domain organizes preferences for a specific application or subsystem. Generally, an application's domain is equal to its General/CFBundleIdentifier (i.e, com.apple.mail, com.omnigroup.OmniWeb).
* User - Most defaults are separated by user so people sharing a machine can have different settings. This is more robust than Mac OS 9's multi-user preference scheme. A mechanism exists for storing preferences that apply to any user (all users) on the system.
* Host - Preference data may apply to any machine, or may be machine specific. Most app preferences are machine independent. For example, a user will want Mail.app to behave the same from machine-to-machine. However, some preferences make sense only for a specific machine. See the Physical Implementation section for more information on how this works.


Again, generally defaults are organized by General/CFBundleIdentifier, are user specific, and are machine-independent. 


== User Defaults API ==

Carbon and Cocoa programs can access the preference system via General/CFPreferences in the General/CoreFoundation framework. General/CFPreferences is a C-level API for reading, writing, and browsing through the user preference system.

Generally, applications will access their defaults in the following ways:

    
 CFStringRef start_page_url;
 Boolean underline_links;
 
 start_page_url = CFPreferencesCopyAppValue(
 					CFSTR("start_page_url"), 
 					kCFPreferencesCurrentApplication);
 underline_links = CFPreferencesGetAppBooleanValue(
 					CFSTR("underline_links"), 
 					kCFPreferencesCurrentApplication, 
 					NULL);
 
 [ ... ]
 
 // the user wants a new default page:
 CFPreferencesSetAppValue(
 	CFSTR("start_page_url"),
 	new_start_page_url,
 	kCFPreferencesCurrentApplication);


(Remember, Cocoa users can use General/TollFreeBridging to replace General/CFStringRefs with General/NSStrings, and so on)

Again, most app preferences are organized by General/CFBundleIdentifier, are user specific, and are machine-independent. 

If you need more flexibility, General/CFPreferences clearly exposes the Domain/User/Machine nature of the defaults system through its more advanced API:

    
 CFStringRef start_page_url;
 
 start_page_url = CFPreferencesCopyValue(
 					CFSTR("start_page_url"), 
 					kCFPreferencesCurrentApplication,
 					kCFPreferencesCurrentUser,
 					kCFPreferencesAnyHost);
 
 [ ... ]
 
 // the user wants a new default page:
 CFPreferencesSetValue(
 	CFSTR("start_page_url"),
 	new_start_page_url,
 	kCFPreferencesCurrentApplication,
 	kCFPreferencesCurrentUser,
 	kCFPreferencesAnyHost);


The Domain, User, and Machine flags are always General/CFStringRef types, giving applications a lot of control. For convenience, General/CFPreferences provides these constants for working with common cases:

    
 CF_EXPORT
 const CFStringRef kCFPreferencesAnyApplication;
 CF_EXPORT
 const CFStringRef kCFPreferencesCurrentApplication;
 CF_EXPORT
 const CFStringRef kCFPreferencesAnyHost;
 CF_EXPORT
 const CFStringRef kCFPreferencesCurrentHost;
 CF_EXPORT
 const CFStringRef kCFPreferencesAnyUser;
 CF_EXPORT
 const CFStringRef kCFPreferencesCurrentUser;


General/CFPreferences also provides a mechanism for getting the list of all available Domains and some functions for deleting unwanted data outright. I will not cover them here. The General/CFPreferences.h header file in General/CoreFoundation includes some good documentation on how to use the API, please consult it for more information on General/CFPreferences.

Foundation also includes an Objective-C class for working with preferences: General/NSUserDefaults. The API sits directly on top of General/CFPreferences. However, General/NSUserDefaults is not a "wrapper" for the General/CFPreferences API. It adds a few extra features, and omits some features as well.

General/NSUserDefaults is generally treated as a singleton class. Here's our example from above:

    
 NSUserDefaults *defaults;
 [NSString start_page_url;
 BOOL underline_links;
 
 defaults = [NSUserDefaults standardUserDefaults];
 start_page_url = [defaults stringForKey:@"start_page_url"];
 underline_links = [defaults boolForKey:@"underline_links"];
 
 [ ... ]
 
 // the user wants a new default page:
 [defaults setObject:new_start_page_url forKey:@"start_page_url"];


General/NSUserDefaults also includes a handy way to tell the preference systems what the default set (think: "Factory Settings") of values for the user defaults should be. General/NSUserDefaults will return one of these values if the user's own defaults doesn't have an entry for a given key. This saves you from having to write out all of your "Factory Settings" to the user's preferences when they launch the app, or from having to check every value returned by objectForKey: (etc.) to make sure something meaningful was present. It really simplifies code.

    
 NSUserDefaults *defaults;
 
 defaults = [NSUserDefaults standardUserDefaults];
 
 [defaults registerDefaults:[NSDictionary dictionaryWithObjectsAndKeys:
 	@"http://www.apple.com/macosx/",  @"start-page-url",
 	@"NO",                            @"underline_links",
 	nil]];
 
 [ ... ]
 
 NSString start_page_url;
 BOOL underline_links;
 
 // the following two calls will return the user's individual preferences,
 // if they've made changes. Otherwise, it will just return the values we
 // registered previously. Saves us some hassle!
 start_page_url = [defaults stringForKey:@"start_page_url"];
 underline_links = [defaults boolForKey:@"underline_links"];


General/NSUserDefaults provides some facility for accessing arbitrary domains as well. However, in my experience, this API is much more clunky than the General/CFPreferences API. I'd recommend Cocoa applications use the General/NSUserDefaults class to access all of their general needs (remember: General/CFBundleIdentifier, user specific, machine-independent). For more advanced work, General/CFPreferences is a much more scalable interface.


== Physical Storage ==

Presently, user defaults are stored in XML property list files named by preference Domain. For example: com.apple.mail.plist. These plists can live in a variety of places on the system, depending on how they are organized by User and Machine:


* $(HOME)/Library/Preferences - General user specific, machine-independent preferences (com.apple.mail.plist)
* $(HOME)/Library/Preferences/ByHost - user specific, machine-dependent preferences (com.apple.screensaver.localhost.plist)
* /Library/Preferences - user independent, machine-dependent preferences (com.apple.PowerManagement.plist)
* /Network/Library/Preferences - user independent, machine independent preferences? **[ General/MikeTrent -- I have no idea if this is true, but it seems logical ]**


You shouldn't rely on the files being in any particular plist format, this is an implementation detail. The storage format could change as we saw with the package receipts in Leopard.

**[ General/ChrisParker - None of the Network domains are implemented in General/CFPreferences or General/NSUserDefaults at this time ] **
 
 
== Tools for Managing Defaults ==

Since the defaults are in XML property lists, enterprising users can edit these files with their favorite text editor. But that's a bit dangerous: the property list format must be perfectly structured, and a little error in the file may make the file unparsable. Enterprising users can also use the PropertyListEditor.app (/Developer/Applications) to open one of these files and mess with it. The PropertyListEditor.app will maintain the XML structure for you, but I find it a little award to use. 

For enterprising users who are comfortable (or want to become comfortable) with the shell, Apple supplies a command called "defaults" that lets you browse and manipulate property lists:

    
 [localhost:~] mike% defaults
 Command line utility to manipulate user defaults.
 Syntax: defaults [-currentHost | -host <hostname>] <subcommand>
 
 Subcommands are:
   read
   read      <domain>
   read      <domain> <key>
   read-type <domain> <key>
   write     <domain> '<domain_rep>'
   write     <domain> <key> '<value>'
   rename    <domain> <old_key> <new_key>
   delete    <domain>
   delete    <domain> <key>
   domains
   find <word>
   help
 
 <domain> is ( <domain_name> | -app <application_name> | -globalDomain)
 <value> is <value_string>
         or -<type> <typed_value>
         or -array[-add] <value1> <value2> ...
         or -dict[-add] <key1> <value1> <key2> <value2> ...


You can use "defaults domains" to find the domain you want to edit. "defaults read" displays a Domain in an old-style plist format (a format invented by General/NeXT and familiar to General/NeXTSTEP, General/OpenStep, Rhapsody, and Mac OS X Server users):

    
 [localhost:~] mike% defaults read com.apple.TextEdit
 {
     NSRecentDocuments = (
         "/Users/mike/Text File.txt", 
         /Users/mike/Desktop/neko/Readme.rtf
     ); 
 }


In a perl-like fashion, curly brackets mean a Dictionary, parenthesis mean an Array, and other scalar values are generally strings. **[ General/MikeTrent -- perhaps someone would like to add an entry for the General/OldStylePropertyList ]** Using the "defaults write" command I can add a new key (if I know what I'm looking for). Using the "-array-add" flag I can easily insert a new file into my General/NSRecentDocuments list:

(This is a little mangled so I make sure it fits on my iBook's screen ;-) )

    
 [localhost:~] mike% defaults write com.apple.TextEdit
             "Mike's Key" "This is dumb"
 [localhost:~] mike% defaults write com.apple.TextEdit 
             General/NSRecentDocuments -array-add /tmp/file.txt /tmp/file2.txt
 [localhost:~] mike% defaults read com.apple.TextEdit
 {
     "Mike's Key" = "This is dumb"; 
     NSRecentDocuments = (
         "/Users/mike/Text File.txt", 
         /Users/mike/Desktop/neko/Readme.rtf, 
         /tmp/file.txt, 
         /tmp/file2.txt
     ); 
 }


**Gambatte Kudasai!**

Hopefully you can see the tremendous value Apple's preferences system adds to Mac OS X. After working with the preference system as a user and as a programmer you too will scorn all apps that "roll-their-own" pref file.

-- General/MikeTrent

*"Gambatte" wa nan desu ka?* --Reply: It roughly translates to "You can do it! / Alright, give it your best!". It's often said right before you're about to try something non-trivial.
----
The new General/CocoaBindings has a special controller for General/UserDefaults.  They have a nice example at: http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaBindings/index.html#//apple_ref/doc/uid/10000167i
----
General/NSUserDefaults is not limited to your own application. You can also use it to get other applications' preferences simply by adding their domain to the search list. For example, to find out where iTunes stores it's database file we could:
    
 NSUserDefaults * defaults = [NSUserDefaults standardUserDefaults];
 [defaults addSuiteNamed:@"com.apple.iApps"];
 NSLog (@"Path: %@", General/defaults objectForKey:@"iTunesRecentDatabasePaths"] objectAtIndex:0]);

-- [[FilippLepalaan

----

While I largely agree with the sentiment expressed by Mike Trent above, one aspect of the General/NSUserDefaults implementation is much clunkier than it needs to be. That's the way you have to "register" a default that doesn't exist. How much nicer it would be to have an API like:
    
 - (id) [valueForKey:(id) key orReturnDefault:(id) value];


So if the default you're requesting doesn't exist for whatever reason, you can specify right there what you'd like it to return in that case. The current approach of having to preregister all these values, or otherwise get a result of 0 or nil is unnecessarily awkward. That said, implementing the above as a category on General/NSUserDefaults (utilizing the registration mechanism internally if you wish) is easy enough, so it's only a minor criticism. --General/GrahamCox

----

I disagree.  The ability to register defaults is much nicer and elegant, especially in cases where a preference may be requested in several source files.  I only have to register once and get it everywhere.  Using `orReturnDefault:` would mean extra typing on each line.  More importantly, it would mean that I'd have to remember to change each and every one if I wanted a different default setting.  If I wanted what you were doing, it's just about as easy for me to read code that says `id x = [ud valueForKey:@"key"]; if (x == nil) x = @"Some default";`.  That's just the way I roll.  Of course, one could write the category as you recommended and then also register defaults and have it both ways.  My opinion, however, is that registering defaults is the very opposite of clunky and I greatly appreciate it. -G

----

I guess it depends on how you typically structure your apps. Usually I find that I only ever read a particular pref in one place so it's more readable if you can define the default where it's used - especially if you read different prefs in a variety of places, since the General/NSUserDefaults only allows an all or nothing registration of a dictionary of defaults - and it doesn't provide a method for obtaining the current dictionary, so making a mutable copy, adding another item and setting it back is not possible - you have to know in advance what every single default you will want to set will be, or else manage your own copy of the dictionary, which partly defeats the object of General/NSUserDefaults keeping it for you. In an app with plug-ins that might want to read defaults, this becomes a real headache, as the app can't know what defaults the plug-ins might wish to register. You end up having to construct your own plug-in protocol for dealing with this, whereas supplying the default value at the point of reading it completely does away with the need for any of this. Also, for numeric values, you can't detect the difference between a non-existent default and one that exists but is legitimately set to zero, so your code above fails for (say) the -integerForKey: method. Is the pref zero, or is it not there?

Anyhoo, as you say, with the category you have it both ways so there's no need to compromise whichever method you prefer. I still maintain that the design could be less clunky than it is, though I concur that if your app is structured just so, it may well work ideally for you. --GC

----

Ok, probably a silly question, but I've look around and couldn't find a definitive answer: is 'defaults' a standard command in OS X - meaning that it doesn't need the developer tools to be installed to have access to it?

----
Yes.

----
*... and it doesn't provide a method for obtaining the current dictionary, so making a mutable copy, adding another item and setting it back is not possible - you have to know in advance what every single default you will want to set will be, or else manage your own copy of the dictionary* --Reply: Not true! You can call -registerDefaults: multiple times to add additional key/value pairs to the General/NSRegistrationDomain. And you can also extract the content of the General/NSRegistrationDomain using:
    
 NSUserDefaults * defaults = [NSUserDefaults standardUserDefaults];
 NSDictionary * defaultValues = [defaults volatileDomainForName:NSRegistrationDomain];

Note though that the General/NSRegistrationDomain seems to come prefilled with a lot of key/value pairs (from General/AppKit?).
While you can overwrite an existing value using -registerDefaults: I see no way to remove a value from the General/NSRegistrationDomain. That seems to be the only limitation.
----
How do you determine which user default has been changed (without painfully checking each one)? Can this information be included in the notification? Examples?
