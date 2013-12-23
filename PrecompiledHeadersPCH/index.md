---
layout: page
title: PrecompiledHeadersPCH
---

This discussion is to help me write an article on PCH files and how to write frameworks with them. With a properly configured PCH file, you no longer have to import hearders in source files contained within a framework target. Some of the questions I have about PCH files are:


*What is the role of the @class keyword. Does the compiler still need these hints for static typing?
*What if any headers are now required for each individual class header (see the next item for a better context of this item). 
*What headers should be imported in the prefix header? Is it ok to import all the headers in a project?
*Should protocols now be precompiled?


--zootbobbalu

