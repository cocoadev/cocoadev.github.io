---
layout: page
title: WebKitGUIEditorMethods
---



Hi all, 

I'm very new to General/CocoaDev and Cocoa itself. I'm currently working on a project that entails webediting.

What I am trying to acheive is atm create a GUI webpage editor using General/WebKit and General/WebView, but limit what you can edit. So far I have created an app that loads a html file on disk as a location then within the General/WebView (with editing:ON) edit it where-ever the user may wish. This is done and detected using the General/WebView and saving is only a matter of getting the source (as General/NSString ) and then saving the whole string to disk. This method works well as it relatively keeps formatting of the source. The program detects changes thru the delgate method webViewDidChange:notification and editing is a little quirky (at ends of paragraphs on writing to the start of a sentence). 

I feel that this method can be problematic, mainly because of the quirky editing, but I also want to edit only certain parts of the webpage (idealy defined by 'id') and remove the quirkyness. I am currently thinking of creating the editor using General/NSDocument, but I dont know where to start. 

I am also new to the DOM* classes used in General/WebKit, it appears to lack documentation and has been so far quite tricky to use. Any view and advice is appreciated. Cheers :)
General/OrlandoZ
