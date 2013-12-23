---
layout: page
title: ObserveValueForKeyPath
---

If you are getting the common error:
*** NSRunLoop ignoring exception **** -observeValueForKeyPath:ofObject:change:context: only defined for abstract class.  Define -[%%%SOME CLASS NAME HERE%%% observeValueForKeyPath:ofObject:change:context:]!' that raised during posting of delayed perform with target 5531a80 and selector 'invokeWithTarget:'


Check to make sure you aren't calling <pre> [super observeValueForKeyPath:ofObject:change:context: ];</pre>  Your superclass is not guaranteed to have implemented this value.  If are calling super, in this situation, it is your superclass that is throwing the exception.

Furthermore, superclasses shouldn't have to worry about KVO of it's children.  They will be independently registered if they need it.

