---
layout: page
title: HowToSupportAppleScript
---


Apple's latest (as of December 2012) guidance on Cocoa AppleScript support is at http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ScriptableCocoaApplications/SApps_intro/SAppsIntro.html

Apple also has the following sample projects.

* SimpleScripting
* SimpleScriptingVerbs
* SimpleScriptingPlugin

and after you've mastered these 3 samples, graduate to the Sketch sample code, which features advanced scriptability, undo, and a boat-load of other stuff.

* Sketch


----

AppleScript is very nice, but coding AppleScript support into your app is not well documented. I hope to fill the gap. 

You should design in AppleScript support from the beginning of your project. You can add it later, but you'll have to compromise the usability of the dictionary. Designing the dictionary and object model is really the hardest part. The rest of it is easy. Apple did a really good job.

Here are some useful links:

*http://developer.apple.com/technotes/tn2002/tn2106.html  The AppleScript HIG
*http://developer.apple.com/documentation/AppleScript/Conceptual/AppleEvents/index.html   AppleScript support with Carbon
*AppleScriptingCocoaApp
*AppleScriptSupport   Some sample code
*DebuggingAppleScriptSupport
*AppleScriptCoercion


Note that AppleScript and Cocoa both use the words *class* and *property*. I will specify Cocoa class or AppleScript class as needed.

DustinVoss

----
**Steps**
----

*1: Design the dictionary.
*2: Write the suite definition files.
*3: Incorporate them into your project.
*4: Handle AppleScript classes, properties, and elements.
*5: Handle AppleScript commands.
*6: For bonus points, support recording.
*Document the dictionary.


I will start by summarizing AppleScript vocabulary:

----
**AppleScript Vocabulary**
----

AppleScript has *dictionaries*, *suites*, *classes*, *commands*,  *events*, *elements*, *properties*, *parameters*, and *specifiers*.

A *dictionary* is all the suites exposed by your app. This includes the core (a.k.a. required, standard, AppleScript) suite and the text suite. A *suite* is a collection of classes, commands, and events. Although the core and text suites are "standard", you can still customize them, because the AppleScript engine only knows about these suites from your application. So your application can redefine the terms in these suites almost as much as it likes.

The *classes* have *elements* and *properties*. If a class has elements, it is a *container* of those elements. The HIG technote listed above has guidelines for deciding whether something should be an element or property, but basically, if there is only one, make it a property, if there can be more, make it an element.

*Commands* are not a part of classes, unlike Java or C++. They are more like Java's interfaces or ObjectiveC's protocols. A command might work on three classes or maybe just one. You cannot vary a command's parameters by the class that is accepting it; instead, document which parameters are valid for each class or create a new command. (I am told this is not true; you can define a command multiple times in an sdef file with different parameters. I have not tried this, however.)

The commands have *parameters*. The parameter immediately following the command is called the *direct parameter* or *direct object*. The other parameters have prepositions or phrases or something in front of them.

*Events* are basically the same as commands, but they are sent from your app rather than to your app. They act like callbacks or notifications.

A *specifier* locates an object within the object hierarchy or an element within containers. The top-level object/container is always the application object.

----
Step 1 **Design the Dictionary**
----

Basically, your dictionary should concentrate on modeling the model, not the view. You need some view-related stuff in there, of course, mostly the selection and windows, but scripters are not focused on that aspect of your app. They want to do work, not screw with outline views.

I say "modeling the model" because you do not want to expose every nut and bolt. AppleScript really is a UI as much as the GUI is, but without the G. Do not include concepts that the user won't want to deal with, or implementation details. Make a mental model for the user to have.

It is easiest to design your Cocoa object model and your dictionary at the same time, with the dictionary taking the lead. But remember that you can always write Cocoa classes that consolidate other classes to provide an friendly AppleScript class.

Also remember that your dictionary's capabilities do not have to match your GUI's. You can allow things in the dictionary that you do not allow through the GUI, and vice versa.

Make sure that you choose vocabulary that flows well, and make use of enumerations.

See the technote http://developer.apple.com/technotes/tn2002/tn2106.html for other design guidelines.

***Terminology***

AppleScript classes, commands, etc., are usually associated with FourCharCode<nowiki/>s. All classes, commands, elements, and properties that use the same word should have the same code, even if the word is used is the standard core and text suites. For example, if one of your objects has a "color" property, it should use the same     'colr' code used in *NSCoreSuite.sdef*. However, this is not true of parameter words. They can have whatever code you like.

Because of a bug in AppleScript, try to avoid naming a property the same as its type. As an example of the bug, say a "rectangle" class has a "color" property, of type "color". If the scripter tries to access the "color" property within a     tell rectangle ... end tell block, he will get an error, unless he says "color of it".

To allow an AS object in a container to be found via name, give it the property "name" with the code     'pnam'. Names are expected to be unique within a container. To allow it to be found via id, give it the property "id" with the code     'ID  ' (that has two spaces, btw).

Other common words and codes are "container" (    'ctnr') and "selection" (    'sele').

See http://developer.apple.com/releasenotes/AppleScript/ASTerminology_AppleEventCodes/TermsAndCodes.html?1 for a complete list.

***Containment***

Make sure all your scriptable objects have a home in the containment hierarchy. An object could be in multiple containers, but it still need one place to call home. As an example, an iPhoto photo can be contained in any number of albums, but its home is in iPhoto's "photos" element.

***Direct Parameters***

Try to design commands so that the direct parameter is the target of the command. For example, design commands to look like one of these (the commands are boldfaced):     **rotate** image 2
**count** a_list
tell image 2 to **rotate**
It is possible to have your direct parameter be a constant instead of a target, as in these:     **say** "hello"
tell window 3 to **zoom** 45 but this gets tricky. See DirectParametersAsValues.

----
Step 2  **Write the Suite Definition Files**
----

An *.sdef* file is an XML file containing an AppleScript suite definition. It includes command syntax, AS classes and properties, documentation, and Cocoa hooks. You can (and should) generate *.scriptTerminology*, *.scriptSuite*, and 'aete' resource files from it. Later, I will go over how to do that.

Run     man sdef for documentation on the sdef file format. It is pretty easy to grasp. You will find a couple of sdef files at */Developer/Examples/Scripting Definitions*.

----
There is a great freeware tool to to edit sdef files in a very OS X way, called Sdef Editor.

http://www.shadowlab.org/Software/sdefeditor.php

The author of this has also put up a nice web reference for the sdef file format.

http://www.shadowlab.org/Software/SdefEditor/SdefReference.html
----

To best way to customize standard terminology, such as the definition of the "application" class, is to copy a sample sdef file (e.g. *NSCoreSuite.sdef*) from the above directory to your project directory, and then edit the copy. If you try to define another "application" class under your own suite code, it won't work.

----
I think I've found the cause of the problems Dustin and I were seeing when defining an "application" class in our own suites. If you write

    
   <class name="application" code="capp" description="My Application's top level scripting object"
       inherits="application">
   ...
   </class>


then     sdp erroneously generates, in     M<nowiki/>yApplication.scriptSuite

    
   <key>NSApplication</key>
     <dict>
       <key>AppleEventCode</key>
       <string>capp</string>
       <key>Superclass</key>
       <string>M<nowiki/>yApplication.NSApplication</string>
   </dict>


which is recursive (plainly     M<nowiki/>yApplication.NSApplication doesn't inherit from     M<nowiki/>yApplication.NSApplication).

If you change the     sdef file to read

    
   <class name="application" code="capp" description="My Application's top level scripting object"
       inherits="**NSCoreSuite.NSApplication**">
   ...
   </class>


then everything will start working.

--AlastairHoughton
----

The sdef     <cocoa> tag specifies the Cocoa classes, methods, and KeyValueCoding keys that correspond to AppleScript words. The tag is optional. If omitted, the sdef parser will supply default class, method, and key names, as follows: each AS word except the first will be capitalized, and the words will be concatenated without spaces. For classes, the first word will also be capitalized. For example, "foreground color" in a script will correspond to "foregroundColor" in code.

The     <cocoa> tag has one XML attribute which will vary depending on which AppleScript thing you are defining, according to the following list:


**Suite definitions* use the     name attribute. Set it to the base filename you want for the *.scriptSuite* and *.scriptTerminology* files.

**Class definitions* use the     class attribute. Set it to the name of the Cocoa class corresponding to this AppleScript class.

**Responds-to declarations* use the     method attribute. Set it to the Cocoa method that CocoaScripting should use to handle the command, for example,     <cocoa method="respondToRotate:"/>. If the command should be handled by an *NSScriptCommand* sub-class rather than the Cocoa class being defined, you still have to declare     <cocoa method=""/>. This is a bug in CocoaScripting.

**Property definitions* use the     method attribute also, but it has a different meaning. Set it to the KVC key to use when getting or setting the property, for example,     <cocoa method="foobaz"/>. See KeyValueCoding.

**Element definitions* use the     method attribute as a KVC key as well. Always include a     <cocoa> tag for elements, because the sdef default does not handle plurals properly; CocoaScripting will end up trying to call accessor methods like **insertInDocumentAtIndex:** instead of **insertInDocumentsAtIndex:**.

**Command and event definitions* use the     class attribute. Set it to the **NSScriptCommand** sub-class responsible for executing the command. The default is **NSScriptCommand**.

**Parameter definitions* use the     key attribute. Parameters get passed to the handler as a dictionary; set this to the dictionary key you want to use for this parameter.


    sdp (the sdef parser) has a bug relating to optional parameters of a command or event. The sdef spec says that the     optional attribute simply has to be present to mark a parameter as optional, and can be set to anything. But     sdp requires that it be     optional="optional" in order to mark the parameter as optional in the *.scriptSuite* file. 

Always include a     <direct-parameter> tag in a command definition, except for commands targeting the application. If you leave it off, CocoaScripting will not be able to find the target of a command like     update items of document (where "update items" is a command without a direct parameter). In this example, you would say     <direct-parameter type="document"/>.

----
Step 3  **Add the Suite Definition Files and AppleScript Support to your Project**
----

CocoaScripting looks at your application's *info.plist* file to check for AppleScript support.

If your application is targeted at OS X 10.3 or earlier, you should also have *.scriptTerminology*, *.scriptSuite*, and 'aete' resources in your project. None of these are built automatically from *.sdef* files. You have to build them yourself with the *sdp* tool. Run     man sdp for documentation.

Here is how to set up your project. Steps 1 and 2 still apply to OS X 10.4, but the others are for OS X 10.3 or earlier.

**1.** Ensure your *info.plist* file has an     NSAppleScriptEnabled key set to "YES". This is a string.

    
   <dict>
      <key>NSAppleScriptEnabled</key>
      <string>YES</string>
   </dict>



**2.** Add your *.sdef* files to the project. They do not need to be members of your application target.

----
I'm having a problem with this... I have the .sdef file (opens fine in Script Editor by itself), have it copied into my /Resources/ directory, and have set NSAppleScriptEnable=YES, and OSAScriptingDefinition=myscripting.sdef ... Script Editor now recognizes my app as scriptable, but opening the library from the app does nothing (i.e. nothing opens), I've triple-checked all key and value spelling, but what else can be wrong? Note that I've done no implementation so far, just the sdef file, but it should still open?
----

**3.** Add a new target to the project. This target will be used to build the *.script**' and aete *.r* files. It should be a shell script target.

**4.** The new target's Shell Script Files build phase should have all your custom *.sdef* files as input files, for example:
    
   ${SRCROOT}/AppleScript/MySuite.sdef
   ${SRCROOT}/AppleScript/NSCoreSuite.sdef


It should have *.r*, *.scriptSuite*, and *.scriptTerminology* files as output files, for example:
    
   ${SRCROOT}/AppleScript/MySuiteScripting.r
   ${SRCROOT}/AppleScript/MySuite.scriptSuite
   ${SRCROOT}/AppleScript/MySuite.scriptTerminology
   ${SRCROOT}/AppleScript/NSCoreSuiteScripting.r
   ${SRCROOT}/AppleScript/NSCoreSuite.scriptSuite
   ${SRCROOT}/AppleScript/NSCoreSuite.scriptTerminology


The shell script itself should run *sdp* on the input files to generate the output files. This example works well:
    
   #!/bin/sh
   cd ${SRCROOT}/AppleScript
   version="-V 10.3"
   
   source="MySuite.sdef"
   include="-i NSCoreSuite.sdef"
   output=`/Developer/Tools/sdp -f ast $version $include $source 2>&1`
   if [ ${#output} -gt 0 ] ; then
       echo $output
       exit 1
   fi
   
   source="NSCoreSuite.sdef"
   include="-i MySuite.sdef"
   output=`/Developer/Tools/sdp -f ast $version $include $source 2>&1`
   if [ ${#output} -gt 0 ] ; then
       echo $output
       exit 1
   fi


These examples were written assuming that you are keeping all AppleScript related files in an *AppleScript* sub-directory of your project, and that you copied the sample *NSCoreSuite.sdef* file there to add your own things to the AS "application" class.

**5.** You will have to actually build the suite definition target before you can proceed. This should result in *.scriptSuite*, *.scriptTerminology*, and *.r* files.

**6.** Add them to the project. They will be put in your Resources group automatically. Make sure they are members of the application target, by making that the active target and checking the check-boxes in the bulls-eye-icon column of the file list.

**7.** Make your application target dependent on the suite definition target. You can edit direct dependencies by double-clicking the target.

**8.** Drag the *.scriptSuite* and *.scriptTerminology* files into the Bundle Resources build phase of your application target.

**9.** Add a ResourceManager Resources build phase to your application target. To do this, select your target, then select *New ResourceManager Resources Build Phase* from the *New Build Phase* item of the *Project* menu. The new build phase will be added as a sub-item of the target.

**10.** Drag the *.r* files into that build phase.

----
I don't believe you need the .r file (and any resulting 'aete' resource) for pure Carbon apps (or is this for 10.1/10.2 compatibility?)
----

If you are delivering an application without a bundle, an aete resource is the only way to provide a dictionary. But for bundled apps, the aete resource offers some dictionary features that the .script files do not, and vice versa. And Tiger supports sdef files directly, so you don't need an aete resource or .script files for a Tiger-only app.

----
Step 4   **Handle AppleScript Classes, Properties, and Elements**
----

CocoaScripting uses KeyValueCoding to get or set properties and elements from Cocoa classes. Oddly, it does not use the standard indexed KVC patterns when dealing with elements. Instead, it uses alternate ones, listed on the KeyValueCoding page.

To handle custom properties of the AS "application" class, you should use the **NSApplication** delegate method **-application:delegateHandlesKey:**. However, if you sub-class **NSApplication**, make sure to change your application's principal class to this sub-class (you can do this from the application target inspector or by modifying the     NSPrincipalClass *info.plist* key) and include the appropriate     <cocoa class="..."/> tag in your *.sdef* file.

Your scriptable Cocoa classes should all implement the **-objectSpecifier** method to return the object's home in the containment hierarchy. In order to do that, you will usually need to tell the object about its container, perhaps with a **-setContainerObject:key:** method. The object should not retain its container.

An implementation of **-objectSpecifier** could look something like this:
    
   - (NSScriptObjectSpecifier *) objectSpecifier
   {
      NSIndexSpecifier *specifier = [[NSIndexSpecifier alloc]
         initWithContainerClassDescription:
           (NSScriptClassDescription *)[myContainer classDescription]
         containerSpecifier: [myContainer objectSpecifier]
         key: @"foobazi"];
      [specifier setIndex: [myContainer indexOfObjectInFoobazi: self]];
      return [specifier autorelease];
   }


The class description, specifier, and key are the container's. In particular, the     key parameter is the KVC key of the container that will lead to your object.

I used **NSIndexSpecifier** in this example, but it is better to use **NSNameSpecifier** if your object has a name property, and best to use **NSUniqueIDSpecifier** if your object has an id property.

To get the application's class description, use     (NSScriptClassDescription *)[NSApp classDescription] or     [[NSScriptSuiteRegistry sharedScriptSuiteRegistry] classDescriptionWithAppleEventCode: FOUR_CHAR_CODE('capp')].

***Read-Only Elements and Properties***

To make an element or property read-only, you must do more than simply mark it as such in your application's dictionary. You must also ensure that the KeyValueCoding methods do not permit modifications.

In the case of a to-one key, this means you must not provide a **-set<Key>:** method and you must not allow KVC to directly modify the variable (which you can do by by returning NO from your **+accessInstanceVariablesDirectly** method or by not having a variable named "<key>").

In the case of a to-many key, you must either return an immutable array from the **-<key>** method, or not implement that method and force CocoaScripting to use the **-countOf<Key>** and **-objectIn<Key>AtIndex:** methods. Note that, despite what the docs say, CocoaScripting does not use **-valueIn<Key>AtIndex:**, although that may have changed in 10.3.

You may want to create a separate set of KVC keys for use by CocoaScripting. These keys would not permit modifications. You could then use another set of KVC keys internally that do allow modifications.

----
Step 5   **Handle AppleScript Commands**
----

CocoaScripting gives you the choice of handling commands in a *command object* (an **NSScriptCommand** sub-class) or in the *target object* (the direct parameter). However, if your command's direct parameter can be a constant  if your command can look like     say "Hello."   then you do not have a choice, you have to use a command object for things to work right. See DirectParametersAsValues.

A command object is the conceptually accurate way to handle an AppleScript command. Here is how you handle a command using a command object:

* Make a sub-class of **NSScriptCommand** and implement     -(id)performDefaultImplementation. This method will perform the command.

* To the sdef command specification, add a     <cocoa class="..."> tag with the sub-class's name.

* The command will be applicable to certain classes. To those sdef class specifications, add     <responds-to> and     <cocoa method=""/> tags. This works around a CocoaScripting bug. If *any* class can be the target, add the tags to NSCoreSuite.sdef's "item" class.


A target object should only handle a command if the object is the command's direct parameter. Here is how you handle a command using the target object:

* Edit the Cocoa class for the target object, and implement a method like     -(id)handleMyCommand:(NSScriptCommand *)command. This method will perform the command.

* To the sdef class description of the target object, add a     <responds-to> tag containing a     <cocoa method="..."> tag with the proper method name (e.g. "handleMyCommand:").


Here is how to write the method that performs the command:

* Make sure you have the required parameters, because AppleScript does not enforce what the dictionary says. If you handling the command in a command object, make sure the target is the right type. If it is not, then set the error number to     errAEWrongDataType and return.

* Coerce the parameters to the desired type. AppleScript and CocoaScripting do not perform any implicit coercions for you, not even simple ones like numbers to strings, or single objects to arrays. Use **NSScriptCoercionHandler** to add your own. If you can't coerce set the error number to     errAECoercionFail and return. More information about coercion is available here AppleScriptCoercion.

* Perform the command and return the result. If there is an error, set an appropriate error code and string.


Here are the **NSScriptCommand** methods that you use to access parameters, etc.:

* **evaluatedReceivers**   Returns the target of the command. This can be nil, in which case the command's target is the application or not known to CocoaScripting.

* **directParameter**   Returns the direct parameter of the command. If the direct parameter is an object specifier, it will not be evaluated to an actual object; use **evaluatedReceivers** instead, or see DirectParametersAsValues for an evaluation routine.

* **evaluatedArguments**   Returns the parameters of the command as a dictionary. The entries' keys are the     <cocoa key="..."/> tags associated with each parameter.

* **setScriptErrorNumber:** and **setScriptErrorString:**   Use these to report an error to the caller. Application-specific error codes should be from 1000-9999. Useful OSA error codes are in the "AppleScript errors" or "Apple event manager" sections of *MacErrors.h*.

* **suspendExecution** and **resumeExecutionWithResult:**  Use these to halt the script while you display a sheet or something and to continue later. If you are handling the command in the target object, you'll need to retain a reference to the **NSScriptCommand** in order to resume it later. You will need to provide an **NSAppleEventDescriptor** instance to **resumeExecutionWithResult:**.



----

In regard to commands..

*If any class can be the target, add the tags to NSCoreSuite.sdef's item class.*

I need to do this, but I don't know how.  My suite is in a bundle, and I don't own the app it loads into.  Something else presumably defines NSCoreSuite, can I safely add to it without overriding something else?  What would the sdef look like?

Actually, the real issue is that my command doesn't use its direct object at all.  I've seen apple events flying around whose direct object is null, how can I get that effect?  I'd like to call it like     my command name "foo" where the name is an argument.

ken

----

The direct object tag is optional in an sdef file. Just don't include it. --DustinVoss

----

That works fine in 10.3, but in 10.2 I get NSReceiversCantHandleCommandScriptError from the app.  I was guessing that I need to declare that the application (or something?) handles the command?  If that's so, then I don't know how, as above.

Ken

----

I worked around the problem by overriding     -[NSScriptCommand evaluatedReceivers] in my subclass of     NSScriptCommand.

    
   - (id)evaluatedReceivers
   {
    if ([self receiversSpecifier] == nil)
    {
        NSScriptSuiteRegistry *sharedRegistry = [NSScriptSuiteRegistry sharedScriptSuiteRegistry];
        if (sharedRegistry classDescriptionWithAppleEventCode:'capp'] supportsCommand:[self commandDescription)
        {
            return NSApp;
        }
        else
        {
            return nil;
        }
    }
    else
     {
         return [super evaluatedReceivers];
      }
   }


This seems to work and is compatible with the docs claimed behavior of     evaluatedReceivers.  I'll post a note if I run into any trouble with this.  Dustin, this page seems to be yours (and a very nice page it is, too); feel free to refactor this area if you want.

Ken

----
Step 6  **Recordability**
----

To support recordability, the app should send itself commands (called direct dispatching). The AppleScript engine will pick up on them and record them. Advice on what to record is at http://developer.apple.com/documentation/mac/IAC/IAC-321.html. To directly dispatch a command, send the Apple Event addressed to a     typeProcessSerialNumber, with the process serial number set to     {0, kCurrentProcess}.

Normally, an app cannot directly dispatch and execute a command that suspends execution. You would have to send the command in the "don't execute" send mode and actually do the work some other way. But I have written a category to allow this. It is posted on my homepage at "http://homepage.mac.com/d.j.v./".

You can use **NSAppleEventDescriptor** to create your commands, but you might find it easier to use  the     AEBuildAppleEvent() function. It is vaguely     printf()-like, and is documented at http://developer.apple.com/technotes/tn/tn2045.html.

Remember to call     AEDisposeDesc() on any     AppleEvent or     AEDesc objects that you create with the Carbon functions.

----

Discussion:

It seems to me like NSAppleScript is the easiest way to send commands to yourself (for recording).  Am I wrong?  Also, I believe NSAppleEventManager can be used to dispatch events to yourself.

----
NSAppleScript may be the easiest way, but the script has to be compiled at run-time before being sent. Using NSAppleEventDescriptor or building an AppleEvent eliminates the compilation step. NSAppleEventManager looks like it can send an already-built AppleEvent, though you'd still have to build it. Also, I had thought that Cocoa apps could not be recorded, but I was mistaken. You just have to address them as I say above.

