---
layout: page
title: SubtleDocumentWindowBehavior
---

Taking a look at how General/TextEdit handles multiple documents, I've found the following subtle behavior when it comes to opening documents:

Case 1: If you launch General/TextEdit without a document, it will create an empty document window titled "Untitled".  If you then open another document (without modifying the empty document), the initial document window contents are replaced with the opened document contents (rather than having a new document window created).

Case 2: If you launch General/TextEdit without a document, then close the initial document window, then create a new document, then open another document (without modifying the new document), it will create a new window for the opened document (and not replace the other document window contents).

In my application, the behavior in both cases is to create a new document window for the opened document.  How do I make it so that my application replaces the initial empty document in Case 1 with the opened document instead of creating a new document window?

btw, my application uses a separate NIB file for the document related objects, and it subclasses General/NSDocument to load documents using the General/[NSDocument loadDataRepresentation:ofType] method.

----
After some research, here's some ideas to work with.

When opening a document, the General/NSDocumentController uses the method in the link below  (use this link as a reference to the steps it takes when making a new document) http://developer.apple.com/documentation/Cocoa/Reference/General/ApplicationKit/ObjC_classic/Classes/General/NSDocumentController.html

The first thing the General/NSDocumentController makes the new document do is - makeWindowControllers. Thus, you must override this function in your General/NSDocument subclass(es). Within it, you must test whether tthe current document is empty or unused (per your description) and then instead of creating a new window, you can pass the data that should be read into the new window into the one that its trying to create.

Here's how to access the General/CurrentDocument:
    
General/NSDocumentController * temp = General/[NSDocumentController sharedDocumentController];
if ([temp currentDocument] != nil) {
  General/NSDocument * current = [temp currentDocument];
  if (-test for emptyness-) {
     -Pass Data from new document to current-
  } else {
    [super makeWindowControllers];
  }
}
  //Rest of testing and closing commands
} 


----

There's a tricky side-effect which isn't accounted for: In Case 1, if you edit the initial document, then undo your edit (reverting to an empty document again), then open another document, it gets opened in a new window.  So just testing for emptiness isn't enough.  I'm thinking there may be something subtle in the document architecture which I'm missing (some function I need to override, some delegate method, or some setting in one of the classes) to get this behavior.  I guess I can scan through the source for General/TextEdit and have a look, I just thought someone here might know offhand.

----

General/TextEdit is not document-based, and so expecting every behavior it exhibits to be found within the General/NSDocument architecture is not a good assumption.

----

aha!  Upon closer inspection, you're correct.  The Document class in General/TextEdit is not derived from General/NSDocument.  Now that I look at Pages, I can see that even if you have an empty document at startup, if you open another document, it gets opened in a new window (different from General/TextEdit).  Thanks!

Though personally I think that I prefer the behavior of General/TextEdit...

----
Another note: If you view the updated API references it will show that loadDataRepresentation:ofType is a deprecated method. You should update to using readFromData:ofType:error:

----

We're targeting our app to run on systems back to 10.2, so that's why we're using loadDataRepresentation.
