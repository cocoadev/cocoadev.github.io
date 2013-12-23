---
layout: page
title: DynamiclyRegisteringDocumentTypes
---

Is there a Cocoa "best practice" for registering the document types an application "knows how to handle" at runtime?

I have an application that has the potential to handle a large number of document types as either an editor, a viewer, or both.
Currently I am placing a number of Info.plist entries to accommodate this with a common document class.

I have adopted a plugin architecture for all of the different formats. All of the plugins adhere to a specific protocol. When a document is opened, all of the plugins are queried if they could handle the document (extensions) and everything is working great.

I don't want to hard-code all of these entries into my Info.plist. I want the ability to distribute the plugins separately. e.g. release a new plugin to extend the application to new formats.

I currently enumerate and query the plugins (more of a driver model really); but I also need the ability to tell the Finder; Oh, BTW I also handle these files beyond my default set, here are my capabilities, icon, etc...

----

There is the new 10.3 LS function LSRegisterURL() (see http://developer.apple.com/documentation/Carbon/Reference/LaunchServicesReference/LSRReference/function_group_5.html), but that reads from the Info.plist. So maybe you could do something *really* nasty like dynamically write to your Info plist (which may very well fail--there is no guarantee that an app can write to its bundle), otherwise, look for something in Launch Services -- it's the sort of thing that LS would handle I think.

----

I would consider subclassing NSDocumentController

makeDocumentWithContentsOfFile:ofType:

- (id)makeDocumentWithContentsOfFile:(NSString *)fileName ofType:(NSString *)docType
Creates and returns an NSDocument object for document type docType from the contents of the file fileName, which must be a fully qualified path. The returned object is not retained. Returns nil if the NSDocument subclass for docType couldn�t be determined or if the object couldn�t be created. This method invokes NSDocument�s initWithContentsOfFile:ofType: and is invoked by openDocumentWithContentsOfFile:display:.

See Also: � makeUntitledDocumentOfType:, � openDocument:

makeDocumentWithContentsOfURL:ofType:

- (id)makeDocumentWithContentsOfURL:(NSURL *)aURL ofType:(NSString *)docType
Creates and returns an NSDocument object for document type docType from the contents of aURL. The returned object is not retained. Returns nil if the NSDocument subclass for docType couldn�t be determined or if the object couldn�t be created. This method invokes NSDocument�s initWithContentsOfURL:ofType: and is invoked by openDocumentWithContentsOfURL:display:.

See Also: � makeUntitledDocumentOfType:, � openDocument:

The NSDocumentController class keeps track of the first instance of NSDocumentController (or a custom subclass) that is created, and returns that instance from its sharedDocumentController method. To get your application to use your custom subclass of NSDocumentController, you must ensure your subclass is the first instance of NSDocumentController created when the application starts up. There are two ways to do this.

-jason
----
Jason,

Thanks but this is not what I am after. I am looking for a "best practices" for registering the document types my application handles at runtime. If I am missing something, please feel free to correct me.

----
I wasn't able to find any hook to "dynamically" register document type information. NSDocumentController does, however, appear to be the holder of such information as well as the creator of new documents. Subclassing it is the same approach I use to enhance and otherwise fine-tune the creation of new documents.

-jason
----
Untested: in your NSDocument subclass implement the class methods +(NSArray*)readableTypes and +(NSArray*)writableTypes to look up a NSMutableArray that holds the list of file types... does this work?
----
It would seem that this is only called when an application open dialog is about to display. Again, I want to programmatically tell the system (Finder) my file associations. e.g. After I lay claim to additional file types, the Finder would use my document icon (if another program was not selected as the default) and the user would be able to double click the files to open them in my application in addition to my application being available in the Finder's "Open With" context menu.
----
It looks like this is essentially impossible short of one or two extremely dirty hacks.
----
Yup, I just spent most of the evening with the Launch Services documentation and a lot of other docs I don't remember seeing before. The funny thing is, the method (hack) "modify your own Info.plist and tell the finder about it" is nearly blessed by Apple. The fact that LSRegisterURL and LSRegisterFSRef exist pretty much says it all.

I put together a method that reads in the Info.plist with the NSPropertyListSerialization/NSData classes, modifies the resulting NSMutableDictionary, reverses the process to write it back out, and then notifies Launch Services that my Info.plist has changed with the afore mentioned LSRegisterURL. Haven't tested it yet but it looks like it should work. I am sure I am going to run into trouble with the permissions issue sooner rather than later though.
----
What you mean is that you wrote **infoPlist = [[[NSMutableDictionary alloc] init] addEntriesFromDictionary:[[NSBundle mainBundle] infoDictionary]] and [infoPlist writeToFile:[NSString stringWithFormat:@"%@/Contents/Info.plist",[[NSBundle mainBundle] bundlePath]] atomically:YES]** right? you know... using the builtin functionality?
----
No, I mean using the NSPropertyListSerialization methods. I tend to code very defensively and don't like leaving loose ends. With the NSPropertyListSerialization methods I can be 100% positive that the Info.plist is written back out in the same format in which it was originally read. Currently, there are 3 different Info.plist formats defined.

> *[NSString stringWithFormat:@"%@/Contents/Info.plist",[[NSBundle mainBundle] bundlePath]*

I use stringByAppendingPathComponent so I don't make an assumption on how the bundle path is formated, and then convert it to an NSURL as the remainder of the methods can use it, and LSRegisterURL will use it toll-free as well.

In a nutshell...

    
- [NSData initWithContentsOfURL:]
+ [NSPropertyListSerialization propertyListFromData:mutabilityOption:format:errorDescription:]

// Party on the NSMutableDictionary...
 
+ [NSPropertyListSerialization dataFromPropertyList:format:errorDescription:]
- [NSData writeToURL:atomically:]

LSRegisterURL...


I also don't like daisy chaining all of the method calls together. If things start to go south during a low-memory condition; I would prefer that my application not blow up in my customers face if it can be avoided.

----

Something that may not be optimal but safe is to possibly have a "plugin installer".  When people download a new plugin, they actually download a little utility, a bundle say, that opens up, shows a progress bar, and behind the scenes finds your app puts the plugin where it needs to go and changes the plist as well.  You can have it so the app is not running when this happens to ensure that you can safely write to the plist.

Just a suggestion, and I hope it helps,

- FranciscoTolmasky
----
Interesting thought, I will mull it over. I guess I could use Authentication Services straight from the application to gain the necessary permissions to perform the changes as well. Authentication Services might be the ticket.
----
It's not just going to be admin permission problems which you'll have. As I said in my (apparently deleted comment, right after "Yup, I ..."), your user's Application folder may be read-only, or they may be in a networked environment where the Application folder is shared. Writing to your own app's bundle is a very fragile thing to do. It appears that my comment disappeared in the 13th revision. Here it is for prosperity's sake:
*I wouldn't go so far as to say it's "nearly blessed" by Apple -- the docs say those functions are designed for installers, etc. Also why are you using NSPropertyListSerialization? when you could just use [NSMutableDictionary dictionaryWithContentsOfFile:]? Permissions will be a big problem. If the user doesn't have write privileges on the Application folder, or the app is used in a networked environment, this wouldn't work. Also keep in mind that -[NSBundle infoDictionary] is not going to be aware of the changed plist.*

----

Yes, 4 minutes after your post it looks like your contribution was overwritten by someone else also questioning my use of NSPropertyListSerialization. It happens sometimes. Unfortunately, the person that overwrote your comments was a bit belligerent about pointing out what they "thought" was obvious.

Thanks for bringing your comments to the surface, I wouldn't have noticed it otherwise.

> *your user's Application folder may be read-only, or they may be in a networked > environment where the Application folder is shared.*

Good point. I can get around a local read-only case and the permissions issues via. Authentication Services, but not the case of a locked down network share or a physically "read-only" media. My application would fail gracefully in this case and forgo updating the Info.plist.

Like I said, I code very defensively and when things start to go sour, it would just skip out of the process in a manner that was appropriate.

> *Writing to your own app's bundle is a very fragile thing to do.*

Trust me, I understand that, again the purpose of my original post was to investigate ways around it. IMHO, I feel that Apple's decision to use what are essentially "static files" for an applications file associations was not a good way to go. It's too bad you can't alternately assign associations in a plug-in's Info.plist as well, it would be optimal. Actually you can add them to the plugins plist, but they don't seem to be picked up by the Finder.

> *I wouldn't go so far as to say it's "nearly blessed" by Apple -- the docs*
> *say those functions are designed for installers, etc.*

The Apple documentation does **NOT** stricly state that the functions are "designed for installers". They only site it as an example. In *Launch Services Concepts and Tasks* under *Registering an Application*; they say:

*It isn�t ordinarily necessary to register an application explicitly with Launch Services, since this is done for you automatically whenever the application becomes known to the Finder, the system is booted, or a new user logs in (see �Registering Applications�). On those rare occasions when you do need to register an application explicitly (such as in a custom installer program), you can use the Launch Services function LSRegisterFSRef or LSRegisterURL, depending on whether the application is identified with a file-system reference or a Core Foundation URL reference. In either case, the application and its document binding information are copied into the Launch Services database, making the application available for opening documents and URLs.*

The last paragraph of *"Registering Applications"* states:

*In spite of these automatic registration utilities, it may sometimes be necessary to register an application explicitly with Launch Services. For example, although developers are encouraged to package their applications so that they can be installed by simply dragging them onto the user�s disk, some applications may require more elaborate custom installer software. In such cases, the installer should call the Launch Services function LSRegisterFSRef (or LSRegisterURL) to register the application explicitly.*

> *Also why are you using NSPropertyListSerialization? when you could just use [NSMutableDictionary dictionaryWithContentsOfFile:]?*

As you may have already seen, I addressed this question above, but I will rehash it here.

The choice of using NSPropertyListSerialization was my call, I saw that I could capture the NSPropertyListFormat the file was originally written with and could be reused for the writing it back out.

I hate making assumptions, and blindly writing "this" file back out (a file my application is not meant to create nor maintain) in what could potentially be an alternate format is something I don't want to do. Example, for whatever reason, if I found the file in NSPropertyListBinaryFormat_v1_0, I certainly wouldn't want to blindly write the file back out in NSPropertyListXMLFormat_v1_0, or whatever the Cocoa default happend to be in the past/future.

> *Permissions will be a big problem.*

Yes, I am also aware of that, I am thinking Authentication Services can get me around that, for the cases other than a read-only media. If the user nixes the authentication, during the process of updating, again, I would fail gracefully.

> *Also keep in mind that -[NSBundle infoDictionary] is not going to be*
> *aware of the changed plist.*

Check... ;-)

It has occurred to me that a user can define explicit file associations in the Finder. In the Finders "File Info" panel, the user can choose an Application to open the file that does not specifically lay claim to the specific file type in question by choosing Other... in the "Open With..." section, and then subsequently (in the resulting open dialog) change the "Enable" selection from "Recommended Applications" to "All Applications".

While my application would ask the users permission before doing so, I wonder if I could run an Applescript that made these explicit associations on the users behalf. That would be cleaner (not modifying the Info.plist) and achieve the same end-result.

Thanks

