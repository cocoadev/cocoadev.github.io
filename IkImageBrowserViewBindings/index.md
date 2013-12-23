---
layout: page
title: IkImageBrowserViewBindings
---

Does IKImageBrowserView work with bindings? I don't see bindings documented, yet I do see that the IB provides them.

If it does, what object does it expect to be given when the model key is sent to the object at the end of the model path?

I have successfully used bindings a number of times and believe I understand NSArrayControllers etc., but cannot make it work in this case, so I suspect there may be something going on I don't know about.

----

Yes, you can bind the dataSource to your controller object.  Then that object must support the IKImageBrowserDataSource protocol.  See the IKImageBrowserView.h for details.

