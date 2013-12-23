---
layout: page
title: NSDictionaryConsoleError
---

I am looking for some debugging advice for an exception raised somewhere in my awakeFromNib.

I am developing a document-based, table-centered app as a learning project, pursuing one tutorial's suggestion to let the user customize
table column configurations. The column configurations are stored in the document model as an NSDictionary object. Attributes include
the column's max, min, and user-specified width, the header-cell title, and a table column identifier to use in connecting table column
customizing operations to other interface controls, or the alignment of the title in the column's header cell. For example, a set of controls
allows the user to turn on and off the display of a particular column in the table by use of a checkbox in another part of the interface.

Everything seems to be working well. The data for the column descriptions are saved into and restored from the document model. If
I resize an individual table column, for example, it is restored properly. It obeys its maximum and minimum width settings. The
user can change the titles in the column header cells at runtime and those settings are preserved. However,
if I resize the window, the rightmost column resizes automatically to fit. If I save the document, and then load it again I get this
exception in my run log, for a notification posted sometime during awake from nib:

*
Exception raised during posting of notification.  Ignored.  exception: *** -[NSDictionary setObject:forKey:]: attempt to insert nil value
*

I emphasize that this exception only appears after a column has been auto-resized by resizing the window. If I specify that all columns
should resize to fit, I get duplicates of this exception, presumably for all the columns that have resized.

AFAIK, the exception is thrown in the window controller's awakeFromNib with interface initialization.There is a property list file in
the app bundle to initialize the set of table column properties for a new document if a test for retrieving the dictionary from the
document model yields a nil object. After the first run, I guess that stuff could also be saved in the user defaults property list.

I suspect I may have some work to do refactoring awakeFromNib. Right now it is a pretty tangled mess. What I need is some advice
about debugging techniques for this kind of bug.

I will publish the relevant code for maintaining the column descriptions below if somebody needs a bribe in order to help me out  ;-)
At this point I am just looking for some advice on debugging strategies to find the object that is posting the notification.

*Look at the DebuggingTechniques page to see how to debug exceptions. You are giving NSDictionary a nil object, whether you believe it or not, and you need to find out what it is so you can fix it.*

----

I recall seeing this problem when I had an NSTableView with an autosave name, but I hadn't given all of the table columns an identifier. It was saving nil as the identifier, and then when it would restore, it didn't check for nil, causing the assertion failure you're seeing. Try making sure that all of your columns have identifiers, even if you're using bindings and don't need it.

Otherwise, you can debug this by setting a symbolic breakpoint on     -[NSException raise]. See DebuggingTechniques for details. Once you have the debugger stopped there, you can examine the call stack and figure out exactly what's triggering it, which should help you figure out how to stop it.

----

Many thanks - the symbolic breakpoint was the ticket. I discovered that I was being a little too fancy with my saving of column preferences,
and was using the delegate method tableViewColumnDidResize: in order to catch any column resizing immediately in the document
model data. That, I guess, was the notification that triggered the exception, because when you open an old document, this notification is
posted as the table draws the right-most column, at least if it fills out the visible table view. However, at that point in waking up, my column
descriptions are being set up, and the first time through, it was seeing a column with a null identifier. Who knows where **that** was coming
from? The identifiers for all nib-defined columns are specified. I was thinking about checking for nil, as you did, which would have solved the
problem, but digging a little deeper with the symbolic breakpoint did the trick and unmasked the culprit.

