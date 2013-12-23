---
layout: page
title: OpenFileWithoutOpenDocument
---

Is there a standard way to open a file without opening a new document in a document-based application?  I want to be able to open files of my document format normally.  On top of that, I want to be able to open other file formats (images in this case) and load them directly to the current document instead of creating a new document.  

What I am considering is subclassing NSDocumentController and overwriting openDocument to do my bidding.

----

A file in a document based application really is a document.

If you want to load a file w/o creating a document, why don't you just load it the old fashioned way?

Remember that the NSDocumentController has one view of how an MDI application should work, if you do not want to follow this view, it might often be easier simply to create your own MDI system (that is what I've had to do for several applications -- often I start out using NSDocument and friends, but in the end it proves to be too inflexible for my needs).

----

What I'm trying to do is get some functionality that is similar to an app like Xcode.  That is, when the user opens a file via "Open" in the "File" menu, different actions are taken depending on the file type being opened.  For instance, when a Xcode file (ext. .xcode) is opened, an entirely new project window is opened.  Contrast that to the user opening a C++ file (ext. .cpp), in which case the file is displayed within the current project window.

Is it possible to do this within the Doc-Based App templete?  I'm just starting out with Cocoa so I'd like to get as much for free as possible.  That's why I'd like to stick with the Doc-Based App templete.

----

You are not limited to one NSDocument class per application.  One could, for example, have a XCTextEditorDocument class for .cpp, .m, .h, .c, .etc... files and a XCProjectDocument class for .xcode documents.  Then, one could simply call NSDocumentController's     openDocumentWithContentsOfFile:display: from one's XCProjectDocument class to create a new XCTextEditorDocument when the user wished to edit a file in the project.  --Bo

----

Is there any skeleton app, tutorial or some real oss software that implements this XCode like functionality using the NSDocument system?
I mean, this should clearly be doable with NSDocument architecture and yet most sample code I've found have resorted to some awkward custom hack.

----

It's not that hard, really.  If TextEdit were done with the NSDocument architecture that would be a good place to start, but unfortunately it isn't.  I don't understand what you mean by "awkward custom hack".  The only thing you would need to do is maybe subclass NSDocumentController so it knew which text-editor documents belong to which (if any) project.

----

I'm sorry if calling it custom hack was a bit inappropriate.  It's just that for a developer just entering the game, anything resembling a document-based app implemented without using the NSDocument architecture seems confusing and "old school". :-)  For me, the hard part is combining windows and documents. Like in the XCode, opened documents may or may not have a window.  And there are other things as well, so it would really be helpful to take a look at the code in XCode or something similar.

----

I think was we're trying to say is that there's no need to forego the NSDocument architecture if you want to do what we understand you want to do.  You can declare multiple document types in your Info.plist (open up the Info window on the Target).  Each document type can be assigned a separate NSDocument subclass to map to; when your application opens a file of the matching type -- through File > Open, double-clicking, drop-on-dock-icon, etc. -- the NSDocument architecture will take care of creating the proper NSDocument subclass that you specified.  If you subclass NSDocumentController, you can customize this behavior further.  You could, for example, track how each document was opened, and associate it with a "master" document if you so desire -- just like XCode.  There's no need to bypass the NSDocument architecture in this case; you just need to extend it.

I'm not entirely sure I understand exactly what you're trying to do here.  Do you just want to give someone the option of importing an image into a document from the File > Open dialog?

----
What you wrote is the way to do it. I know, but now how to do it. Do you have or is there any skeleton app, template to start with with the functionality using your NSDocument system? For me, and I think many others , do have to see it and then we can go on. 
SC

----
Go read the BreakItDown page for help if you're feeling overwhelmed. Then apply that strategy by defining some steps you can take toward your goal. For example, try to add another NSDocument subclass to your app, so that you can open two different types of documents. And then maybe add a third type as well.  this work will give you some hands-on experience that will help you when you're ready to tackle the task of associating your 'master' document with it's various sub-documents. And if you get stuck somewhere, you'll be able to come back here and ask more specific, better-defined questions. -CS

