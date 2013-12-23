---
layout: page
title: CustomHeaderComments
---



Peter Hosey posted an article at http://boredzo.org/blog/archives/2006-03-28/how-to-evict-__mycompanyname__-from-their-office-building showing how to get rid of that pesky __MyCompanyName__ text from the comments section at the top of new Xcode documents (he in turn found the information in the book "Step Into Xcode" and the Xcode release notes page).

Perhaps a better way is to modify the actual template but as he points out any update to Xcode is likely to break these changes. However, taking advantage of the order in which Mac OS X searches different locations for settings we can create our own local templates in "~/Library/Application Support/Apple/Developer Tools/File Templates" and Xcode will use them in preference to the system wide templates found in "/Library/Application Support/Apple/Developer Tools/File Templates". Just copy the template you need, make the desired changes (including removing ORGANIZATIONNAME if you wish) and next time you launch Xcode and create a new file it will use your template instead of the default one.

--Matthew LINDFIELD SEAGER

[ Edit: I just found out I'm not the first to discover/publish this! =( Stig Brautaset published this over a year ago: http://blog.brautaset.org/2006/03/04/custom-file-templates-in-xcode/ ]

