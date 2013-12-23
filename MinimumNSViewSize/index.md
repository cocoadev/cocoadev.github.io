---
layout: page
title: MinimumNSViewSize
---

Hi,

I would like to make minimum size custom nsview can scale. I tried to subclass setFrame, but my app always crashes. Could somebody give me example on how to implent setFrame in my subclassed nsview?

Thanks

----

Don't.  Implement -resizeWithOldSuperviewSize: instead, ... But don't do that either.  What you really want to do is either set a minimum window size in InterfaceBuilder or implement -windowWillResize:toSize: in your window's delegate and use that to keep your window from smooshing your custom view.  If you only control your view's minimum size, it's superview will still get smaller and clip your view anyway, probably not what you want.  -- Bo

