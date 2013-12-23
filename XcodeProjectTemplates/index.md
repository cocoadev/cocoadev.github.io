---
layout: page
title: XcodeProjectTemplates
---



Xcode (and Project Builder before it) has project templates which are presented when creating a new project. One of the neat features of such templates is the ability to automatically insert stuff, like the author's name, or the title of the project. Project templates are located in /Library/Application Support/Apple/Developer Tools/Project Templates/. 

Referring to the General/CocoaApp template from Apple, here's what the main.m file looks like before you make a new project based off of it:
    /*
 *  main.m
 *  �PROJECTNAME�
 *
 *  Created by �FULLUSERNAME� on �DATE�.
 *  Copyright (c) �YEAR� �ORGANIZATIONNAME�. All rights reserved.
 *
 */
As you can see, it uses tags which are replaced when the project is made. These can also be in target and file names, too. On the left side of each tag is �, formed by option-\. On the right side is the reverse (�), formed by shift-option-\. (Note: The symbols, which look like << and >>, are in the UTF-8 encoding on this page). There are several other tags, described in General/ProjectBuilderTips. I will list them here for convenience:


*�DATE� *Current date (using General/NSCalendarDate format "%x")*
*�DIRECTORY� *Full path of the file's parent directory*
*�FILENAME� *Full file name, as typed by user*
*�FILEBASENAME� *File name without the extension*
*�FILEBASENAMEASIDENTIFIER� *File name without the extension, mangled to a legal C-style identifier*
*�FILEEXTENSION� *Current file's extension*
*�FULLUSERNAME� *Full name of the logged in user*
*�PROJECTNAME� *Name of the project to which the file was added (blank if none)*
*�PROJECTNAMEASIDENTIFIER� *Name of the project, mangled to a legal C-style identifier*
*�PROJECTNAMEASXML� *Name of the project, as a valid XML string*
*�TIME�*Current time (using General/NSCalendarDate format "%X")*
*�USERNAME� *Account name ("short name") of the logged in user*


General/JeffDisher adds that in order to tell Xcode which files to search for such tags in, you have to add a General/TemplateInfo.plist file inside your .xcode bundle. For an example of how to do this, look into the General/TemplateInfo.plist file stored in one of the project templates from Apple. The .plist file contains your template's description, and files which need to have the tags replaced within them and in their names.

In order to set the name of the executable, you need to add a Info.plist file which has �PROJECTNAMEASXML� set as the value for the General/CFBundleExecutable string. Add "Info.plist" to the General/TemplateInfo.plist file as one which needs tags expanded.

--General/RyanGovostes

----

I (Aaron Harnly) have created a simple script for generating an Xcode project template from a project. You simply create the project in the normal fashion, using the project name itself (e.g. General/MyCoolProject for a project file named General/MyCoolProject.xcodeproj) wherever you want the templated project name to appear. It seems to work, but almost certainly needs improvement. The script can be found here:

http://harnly.net/software/xcode-template-maker/

----

Similar info, with details of how to change the �ORGANIZATIONNAME� for example, can be found at 

https://uimon.cern.ch/twiki/bin/view/Atlas/General/AthenaWithOSX

--General/ShaunRoe

That link seems to require a username and password, but since you posted the page on General/MacOSX-dev, I post your post here:

*
Some of these variables, such as �ORGANIZATIONNAME� need to be set by you. To do this you can change the General/XCode preferences by editing

    ~/Library/Preferences/com.apple.Xcode.plist

(double click on the file to open the plist editor) and change the entries associated with General/PBXCustomTemplateMacroDefinitions (create one if it does not exist). Alternatively, you can enter the following command in the terminal app:

    defaults write com.apple.Xcode General/PBXCustomTemplateMacroDefinitions '{ "ORGANIZATIONNAME" = "My Company";}'
*

----

I just installed version 1.2 of Xcode and opened a test project,  the Cocoa Application type.

First thing I noticed was a compile warning on building the empty project, about an incompatible pointer type in the argument
list for General/NSApplicationMain in main.m. No problem: I looked at an old project and saw that the declaration of main( ) was

    int main( int argc, const char *argv[ ] )

whereas in the new 1.2 project it was

    int main( int argc, char *argv[ ] )     // argv needs to be declared const char * argv[ ] 

You can correct the declaration in the project template in the /Library/Application Support/Apple/Developer Tools/Project Templates folder.

----
Anyone knows if it is possible to create a file template on a project basis (preferably stored within the project folder)? When I write code for an open source framework, it would be nice if the file automatically has the framework name, licence, etc. at the top of each source file. But if I write for another project, I might want some other text.

thanks,

General/KoenvanderDrift

----
Koen,
This seems to work for different templates for your project:

Go into the Templates folder and add a new folder where you want to have it. e.g.
    /Library/Application Support/Apple/Developer Tools/File Templates/Cocoa/Objective-C class.pbfiletemplate

already exists and can just be duplicated and adapted. e.g.
    /Library/Application Support/Apple/Developer Tools/File Templates/Cocoa/Objective-C class General/OpenSource.pbfiletemplate

And there you have it then in the "add file" dialogue. But I agree that this is a bit complicated approach to this whole thing. ;)

_njyo

*Actually, you can use ~/Library... for this, to avoid polluting other people's namespaces.*

----

In case anyone is interested, here is a really rough project for creating and editing General/XCode project templates and file templates:
http://www.spectralclass.com/downloads/xtemplate.dmg.gz

The source is provided on the image under the MIT License.  Take a look and tell me how it works out,

--General/JeffDisher

----
Macro expansion inside the .pbxproj file itself doesn't seem to work. When I add a macro, Xcode reports that the file is missing.
Is this a known issues or is there a workaround ?

Thanks,

Adriaan van Os

P.S. Sorry, I now found the answer myself. The Xcode error message is misleading. Macro expansion inside the .pbxproj file
works, but the characters needed around PROJECTNAME depend on the character set of the file. They are not the same as
in Xcode itself. 

To be precise: The .pbxproj file must be in UTF-8. Other files that you specify for macro expansion must be in General/MacRoman. Yes, this is annoying.

----
I just created a mini tool that can help with the process of creating and maintaining Xcode templates:
http://hogbaysoftware.com/products/xcodetemplatefactory

--General/JesseGrosjean

----
I just read on stackoverflow that instead of setting your company's name by the     defaults-command, you can also set the �ORGANIZATIONNAME� tag by editing your me-card in addressbook.app to state at the Company field the name of your company! No fiddling with the Terminal with hard to remember arguments etc. 

-- Redmer

----
A few people seem (like me) to have had trouble with the project templates in Xcode 3.2. You probably don't have the problem unless you can create a new Cocoa Application project with no app delegate class. If you do, I've written up how to fix it: http://effectif.com/articles/xcode-template-problems. http://goo.gl/General/OeSCu

-- Graham Ashton
