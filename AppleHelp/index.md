---
layout: page
title: AppleHelp
---



See the article at http://www.oreillynet.com/pub/a/mac/2001/05/25/mac_help.html for information on how to get basic Apple Help working for your application. 

Apple now provides information on registering Apple Help for Mac OS X applications at http://developer.apple.com/documentation/Carbon/Conceptual/ProvidingUserAssitAppleHelp/index.html#//apple_ref/doc/uid/TP30000903

and see http://developer.apple.com/documentation/UserExperience/Conceptual/OSXHIGuidelines/XHIGUsingTechnologies/chapter_8_section_13.html

See http://www.andymatuschak.org/articles/2005/12/18/help-with-apple-help for a lengthy article on getting Apple Help working and looking like Apple's help books.

See HelpbookCreator at http://robin.mascue.de/index.php?pageid=13 . Application for creating help books.

See http://www.andymatuschak.org/pages/helptoolkit for HTML/CSS templates based off Apple's help books.

(The information in the section is officially documented at http://developer.apple.com/documentation/Carbon/Conceptual/ProvidingUserAssitAppleHelp/index.html ).

Caveats about AppleHelp. Your help files should conform to HTML 3.2 for best results and in order to use the Apple Help Indexing Tool (part of CarbonLib SDK) you will need to set both the creator and file type attributes on your html files to 'hbwr' and 'TEXT' respectively. Lastly, make certain that the Info.plist key NSHelpFile does not exist in your plist.

----
Apple help HTML tricks:

The first page must include the meta tag 
    <META name="AppleTitle" Content="MyApp Help">

the AppleTitle needs to be the same as your CFBundleHelpBookName plist key.

If you want to add some keywords to your page top help the search function (I'm not sure how this works):
    <META name="keywords" Content="keyword1, keyword2, keyword3">
If you want to exclude your page from any search results (for eg: the table of contents):
    <META name="robots" Content="noindex">
If you want to a brief description of the link to appear in the search results:
    <META name="AppleAbstract" Content="This is the topic you are looking for!">
----

Links in the HREF:

Haven't tried these yet please fill the blanks...

????
help:goto_helpcenter=user

To open another helpbook from your help page

help:openbook=MyApp%20Help

To go directly to an anchor in a help book (you need to have anchor indexing turned on in the indexing tool , available in the CarbonLib SDK by the way)

help:anchor=p001 bookID=MyApp%20Help

perform a search fro a link in your help page

help:search='find files' bookID=MyApp%20Help

To run aa applescript from the help page
help:runscript=MyApp/Help/script

----

If your Help Viewer unexpectedly quits throughout your osx system, I believe the solution is to delete the files in ~/Library/Documentation/Help/ . This happened to me when we were trying to figure out how to use Help Viewer. One of the aliases was pointing to a bad help file.

----

I followed the tutorial at http://cocoadevcentral.com/articles/000072.php with some success. I've added the help pages under "Resources" in Groups & Files inside a folder named "MyApp Help". When I compile, the contents of this folder ends up in the root of resources (package contents, mac os, resources) and not in a folder with the name "MyApp Help" and thus utterly fails.

I can manually create a folder in the package and move the files, then it works. But how do I tell Xcode to create a folder in Resources instead of putting everything in the Resources root?

----

I think you probably just forgot to use the option of creating folder references when you added the folder containing the help files to the project. This has not changed with Xcode 2.x. When you create the folder references, the files will be added to your bundle's Resources folder in a folder named "MyApp Help".

----
This is at http://developer.apple.com/documentation/DeveloperTools/Conceptual/XcodeUserGuide20/Contents/Resources/en.lproj/pr_add_files/chapter_7_section_5.html#//apple_ref/doc/uid/TP40001440-CH221-BCIDAGFI and solved the problem!

----
To add a directory/folder to the Resources Group, the above reference provides a good hint as to what should be done although it points to now out-of-date documentation.  The steps that worked for me are:

1. Create the directory in the "English.lproj" directory, then add the files/subdirectories to it.  Or simply copy in the directory from somewhere else.  If the native language for the documents are not English, then the directory should be put in the appropriate ".lproj".

2. Control-click the "Resouces" directory in the Xcode project and then select "Add" and then "Existing Files...".

3. The above should bring a up a file selection window.  Navigate to the ".lproj" directory used in step 1 and select only the directory you just created there.

4. Next another dialog will come up.  Here you select two things.  First, you must select the "Create Folder References for any added folders" button.  Second, you should only select the Targets that apply to the generated Application Bundles/Frameworks (ie you might want to deselect any unit tests if this is not applicable to them).

5.  That should be it.  You should now see the folder and its files/subdirectories added to the Resouces Group and under the appropriate language.  Although I have not tried it, the documentation states that when you add a language, xcode will automatically copy your directory that you just added  to the new ".lproj" directory.

Hope that helps.

----

Don't create things directly in the .lproj folders.  Instead, create it in the language-neutral location and enable localization on the Get Info panel.

----

I would agree with not creating things in the .lproj folders normally, but you can not enable localization of a folder/directory on the Get Info panel( or at least, I could not figure out how to).  After you perform the steps above, the directory shows up in the Resources group with its contents being in the English category.  If you do a Get Info on that directory/folder, it shows up as localized to English and allows you to add other languages.  If there is a better way, then I would like to know it.  In my case, I was adding a help folder.

