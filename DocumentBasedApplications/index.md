---
layout: page
title: DocumentBasedApplications
---



The architecture of DocumentBasedApplications:


* An NSDocumentController owns as many NSDocument objects as necessary for the currently running application.  NSDocumentController very rarely needs to be subclassed.

* Each NSDocument owns one or more NSWindowController objects.  DocumentOriented apps must have at least one NSDocument subclass.

* Each NSWindowController owns a single NSWindow.  NSWindowController objects are frequently subclassed, except in extremely simple apps.

* NSWindow usually does not need to be subclassed, if you are following the ModelViewController architecture.  Most important custom window behavior can be implemented by a delegate.


The default NSDocumentController creates instances of your NSDocument subclass in response to user actions, such as choosing File -> New from the menu.  NSDocumentController knows the name of your subclass from the "Document Types" settings in the "Application Settings" tab of ProjectBuilder's "Edit Active Target" pane.  The information in this pane ends up in your application bundle's Info.plist.  You need to define at least one Document Type, and set its NSDocumentClass to the name of your NSDocument subclass.

[add information about when documents and window controllers are released]

[add information about how a document creates its window controllers]

[add information about how a window controller loads a NibFile containing its window]

For more information on this topic refer to:

/Developer/Documentation/ReleaseNotes/NSDocumentFAQ.html

...and the reference page for NSDocument.

To add a page to the list below, create a new page and put the following anywhere within it (or add to an existing page):

    \\%\\%BEGINENTRY\\%\\%DocumentBasedApplications\\%\\%ENDENTRY\\%\\% 

You do **not** need to edit
*this* page.

Here are some pages that discuss matters related to DocumentBasedApplications.

[Topic]

