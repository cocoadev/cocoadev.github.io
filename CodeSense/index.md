---
layout: page
title: CodeSense
---

CodeSense is Xcode's code autocompletion feature. It's rather spiffy (and speedy in Xcode 1.1), but sadly doesn't work with Java.

----

Note that there is a per-project code sense setting that overrides the global one.  Do a 'Get Info' on the project icon (the very top icon in the project window's outline) and look under the code sense tab.  Both indexing and code sense need to be enabled there.  There's also a 'Rebuild Index' button that might help if there's a problem with the index.   -- Bo

----

Tool that complements code sense really nice. 

http://www.obdev.at/products/completion-dictionary/index.html

----

Does anyone know if it's possible to use Code Sense or the Xcode indexes outside Xcode (or if Code Sense/indexing is based on an OSS project)? I see the `xcodeindex` command and it can dump the indexes, but maybe it's available in another form also? E.g. the dump doesn't include line numbers.

