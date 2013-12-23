---
layout: page
title: CoreDataApplicationNewVersion
---

I am coming up to speed on CoreData in Tiger, and I have written a little application that I'd like to release.  I am worried that when I release the application, I might want to change the datamodel in future versions.  This would invalidate user data saved in the original application version.

Is there any documentation available on how to move Managed Objects from one version to the next?  Does anyone have any ideas how to do this?

*

There is a discussion about this on the Cocoa mailing list at http://www.cocoabuilder.com/archive/message/cocoa/2005/5/6/135088.

I'm experimenting writing model migration scripts with CoreDataExplorer and FScript. Might be a viable solution. 

*

