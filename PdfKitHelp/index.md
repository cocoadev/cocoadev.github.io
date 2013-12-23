---
layout: page
title: PdfKitHelp
---

I'm trying to write a simple pdf viewer using cocoa's pdf kit. I created a project trying to emulate the PDFKitViewer example, but when I try to open a pdf file through the menu (command - O) with my application the file names are grayed and I'm unable to select a file. How do I do this? I'm new to cocoa, thanks in advance.


----

My first guess is that you haven't set your application's info.plist to say that you can deal with PDF files as an editor or viewer.

If you open the PDFKitViewer (or PDFLinker2) in XCode and then select "Project" "Edit Active Target", it'll bring up the "Target Info" dialog. In the "Properties" pane, there'll be a table of "Document Types".

Make sure your project has an entry in the table with similar information, choosing a role of "Editor" or "Viewer" as desired. I hope that will solve your problem.

-- Elmer  (Who's still wishing for help with his EditingPDFMetadata problem.)

