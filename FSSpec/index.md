---
layout: page
title: FSSpec
---

FSSpec is a structure used to refer to a file on disk. In this respect it works much like a path.

FSSpec<nowiki/>s are deprecated on Mac OS X and their use is highly discouraged. They have trouble with internationalization and cannot support some files with more than 31 characters in their name. Avoid them at all costs.

The modern replacement for FSSpec is FSRef. Unlike FSSpec, FSRef cannot refer to files which do not already exist. Old APIs which took an FSSpec to specify a file which may not exist are usually replaced with an API which takes an FSRef to the paret folder, plus a name of a file inside it. This FSRef/name combination provides equivalent capabilities while avoiding the problems of FSSpec<nowiki/>s.

One very common use of FSSpec<nowiki/>s on OS X is in calling QuickTime, which still has a lot of FSSpec APIs. Apple has a technote, "Modernizing QuickTime Applications", which describes alternatives to these APIs. This technote can be viewed here: http://developer.apple.com/technotes/tn2005/tn2140.html

