---
layout: page
title: AHIGHypocrisy
---

Do read the Apple Human Interface Guidelines found at http://developer.apple.com/documentation/UserExperience/Conceptual/OSXHIGuidelines/index.html

After doing so, you might be a little frustrated that Apple do on occasion fail to honor these guidelines. This is no excuse for you to do the same, but this page serves as an outlet to document Apple's apparent lack of respect toward their own guidelines.

**Once a menu is opened, it remains open until another action forces it to close. Such actions include: [...] A click outside the menu* � with Panther, a right click outside the menu will actually fall thru to the application.

**a standard document window has the following attributes [...] A proxy icon* � Apple generally forgets about this attribute (QuickTime, iTunes, ...).

----

*iTunes is not a document-based application, and neither is QuickTime Player if you have the 'open movies in same window' preference checked.*

**I agree that iTunes is not really document based, but it could gain from treating the currently selected file as its document, just like Xcode does.**

*The current playlist would be appropriate.*

The currently playing or currently selected sound file would be equally appropriate. In other words, iTunes is not really a document-based application.

**QuickTime OTOH really is document based (from the users point of view), even though you can set it to open new documents in the same window, this does not take away the ability to have many documents open, create new untitled documents (where the first one is sadly named "Untitled 1", which is also depicted in AHIG as wrong), manipulate and save individual documents or parts of same etc.**

Beginning with Mac OS X 10.4 Tiger, QuickTime player windows have a proxy icon. -- l0ne

----

**Don�t use the brushed metal look indiscriminately [...] For example [...] it does not work very well for the TextEdit document window* � how does TextEdit really differ from e.g. Safari? both are document based applications which mainly show one big textual document...

----

*You can edit and save TextEdit's documents, for one. And I wouldn't really consider Safari document-based - it's an information browser, like Finder in its metal-window mode. Although I agree, HIG is fairly messed up when it comes to metal windows.*

**So if Safari could also edit the HTML (which wouldn't be far fetched), nothing visually would change, but then the metal look wouldn't work? and Safari really is document based, it just only has view capabilities, but double click 10 HTML documents, and they open in 10 new Safari windows, each with their own path, each allowing you to save it, each representing a document etc.**

*unless you have open external links in tabs turned on...*

**Only the main window of an application is suppose to have texture, so it Safari gains HTML editing, the edit window would be pinstripe just like the source view now.**

From the AHIG (textured windows): "*...has been designed specifically for  use by�and is therefore best suited to�applications that provide an interface for a  digital peripheral, such as a camera, or an interface for managing data shared with  digital peripherals...*" When the a Finder window has texture (when showing the toolbar), you have access to iDisk, iPod, servers, a digital camera, etc.., via the sidebar, therefore the use of texture is in accordance with the AHIG. The same also goes for Safari.

By that line of reasoning anything that saves a file to a disk should be metal.  "interface for managing data shared with digital peripherals...'"

The thing about that is that you have to use some common sense when interperting the guidelines, not just saying that any file which saves to a disk would should be metal if Finder and Safari can.
----

**If all of the items in a menu or submenu are unavailable, the menu title is dimmed* � this is not even possible with Cocoa's automatic menu validation scheme.

----

*This sounds like a bug in the HIG... It also says the user should be able to open and inspect the menu's contents. If it's possible to open or interact with something, it shouldn't appear disabled.*

----

**Name a new document window �untitled�; leaving it lowercase* � unfortunately NSDocument returns a titlecase version (which is even depicted as **wrong** in the AHIG).

*see HowToLowerCaseNewDocUntitledTitle for a solution, if this keeps you awake at night*

*[drag and drop] *When the destination can accept all of the dragged items, the destination should accept them in the order specified by the source. The source application should organize the dragged items in the order in which they were selected, except in two cases. If the dragged items come from ordered views (such as View by Date or an alphabetized list), that view�s ordering takes precedence over the selection order* � unfortunately the Finder places items on the dragging pasteboard in what seems to be an arbitrary order.

*[Menus] *A bullet indicates that the document has unsaved changes* � unfortunately Cocoa doesn't allow for this, nor does NSDocumentController take care of it (as the AHIG says).


----

*Make sure you bring these issues to Apple's attention via* http://bugreport.apple.com/

Can anyone report any success with the bug reporter? I have around 20 bugs still as Open/Analyze, and they are serious stuff, such as GCC producing broken code, crashes in the Cocoa frameworks under certain conditions, which are not directly avoidable by the application programmer and so on. But sending an inquiry to DevBugs@Apple.Com just returns a "standard" reply about their engineering team being busy :-\ so such style guide violations which Apple is infamous of, probably have even lower priority.

(For the sake of comparison, check out the MSDN Product Feedback Center for Visual Studio 2005: http://lab.msdn.microsoft.com/productfeedback/)

----
I sent in a few bug reports from an developer preview build of Panther that made their way to the Darwin team, and I have gotten responses stating that issue had been resolved.  And through my own testing, I know that it has been.

----

Does the "New" Tiger Mail.app interface (toolbar) make anyone else vomit and convulse on the floor?

http://www.apple.com/macosx/tiger/mail.html

*Well, I get the feeling that the images will change again (as Mail.app's interface has changed several times over the life time of the Tiger DP's). Plus, those images seem to only be the small versions, the larger icons probably look better.

Jacob*

I'll take the toolbar in exchange for getting rid of the drawer

*YES! Get rid of the bulky drawer. They just need to get rid of that component all together from Cocoa.*

Drawers are fine for their intended purpose - as a sort of nonmodal sheet. But they're way overused. And, anyway, check the screenshot on apple.com - it's gone from Mail, at least

