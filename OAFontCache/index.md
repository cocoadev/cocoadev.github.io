---
layout: page
title: OAFontCache
---



OAFontCache is a nice program-wide solution to the problem that getting NSFonts is slow. (very slow). It supports lookups of NSFonts by family, name, and other attributes.

It also has protection against multiple threads looking up fonts and changing the table at the same time. As a relatively simple class, it is a good example of how to use NSLock.

