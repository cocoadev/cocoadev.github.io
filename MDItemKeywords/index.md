---
layout: page
title: MDItemKeywords
---

Does anyone know how you can get the words that a single file provides to SpotLight for indexing? After playing with     MDItem I figured out that     kMDItemKeywords is only used for "KEYwords" and not for indexing the file. 

----

Do you mean the metadata/attributes of the file?

----

If you mean *ALL* possible metadata attribute keys, see: http://developer.apple.com/documentation/Carbon/Reference/MDItemRef/Reference/chapter_1.3_section_2.html

----

No, what I'm talking about are the symbols that are matched to the text searches that get performed on a SpotLight search. 

Simple example. Let's say you have a source code document     MyClass.m. This file will be indexed by SpotLight and then become searchable by the SptLight database. What I'm after are the words that SpotLight has added to it's DB.

