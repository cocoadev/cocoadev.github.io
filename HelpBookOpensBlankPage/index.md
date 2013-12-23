---
layout: page
title: HelpBookOpensBlankPage
---

 see also the pages AppleHelp (about how to register your help pages with the Help system) and HelpViewer

Some weeks ago I wrote the initial help book for my application. I placed a �CFBundleHelpBookName = "MyApp Help"� entry in my InfoPlist.strings and a �CFBundleHelpBookFolder = "MyApp Help Book"� entry in my info.plist.

I have an index.html (in the "English.lproj/MyApp Help Book" folder) which contain the following line: �<meta name="AppleTitle" content="MyApp Help">�.

I have dragged the help book folder to the AppleHelpIndexingTool to generate an index.

It all worked fine when I created it and tested it -- but then today, it just shows a blank page when I open the Help Viewer (both with the default showHelp: action method, and when asking for a specific page with AHGotoPage()).

----

Are you sure it's a blank page? HelpViewer is awfully slow. I think it's just showing a blank page every time it opens.

----

I've experienced something like that, removing the HelpViewer cache (or something) in ~/Library helped. It happened when I chaged the AppleTitle name between version of my software.

----

I get this question sometimes from my customers. I tell them the following and it always resolves the issue: There are a few annoying bugs with the help viewer's caching.

Check discussions.apple.com (discussions forum) and search for "Help Viewer crashes or shows blank win"

 - JNozzi

----

Thanks for the suggestions! It turned out that the problem was that I had placed this in one of the pages:
    
<meta name="AppleTitle" content="Bundles">


This was because I thought that I could then use NSHelpManager to bring up that specific section of my Help Book, referring to "Bundles" as its name.

Instead the help viewer then thought my entire book was called Bundles, and would ignore the second AppleTitle meta tag in my table-of-contents (callling it "MyApp Help").

----

Apple discusses how to register an application's Help book at 

http://developer.apple.com/documentation/Carbon/Conceptual/ProvidingUserAssitAppleHelp/index.html#//apple_ref/doc/uid/TP30000903

----

I had the problem where the help files wouldn't open from my application but would open fine in Safari or in Help Viewer if I said to open the file using Help Viewer. I had the correct keys in the info.plist file, added the right META tags, indexed the files and it still wouldn't work. It was very frustrating,and Help Viewer wasn't helping much.

Finally remembered that I had set TextWrangler (which I use to create the html files) to save the files using Unix-style line feeds for eol and not Mac-style carraige returns. On a whim I changed TextWrangler to use CR, opened and saved the main index.htm file, ran my app and hit cmd-?. Voila! There was my help file, with all the links active and working. This is interesting, since the other files in my help still have LF's in them. It's only the first page that needs CR's, and without them Help Viewer will not open the page.

Problem solved!

----

There are actually a few different possible causes for this problem, but the most frequent cause is a bug in Help Book as JNozzi mentioned. It will work fine most of the time, but every now and then it'll just show a blank page. I *believe* this bug has been addressed quite awhile ago, however.

----

Another possible problem, when you add a line with a tag attribute (such as <meta name="A<nowiki/>ppleTitle" ...) make sure you use ONLY DOUBLE quotes, even though HTML version 3.2 allows both single and double quotes help viewer wasn't happy and I've just spent an hour working that out...

