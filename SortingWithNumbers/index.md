---
layout: page
title: SortingWithNumbers
---

I have an NSArrayController that controls the objects of an array and displays them in a NSTableView. To sort the objects I set the NSArrayController's sort descriptor to a default ascending sort descriptor. The problems is that the objects are being sorted like this:

Object 1
Object 10
Object 11
Object 2
Object 3
...

And I would like them to be sort like this:

Object 1
Object 2
Object 3
...
Object 9
Object 10
Object 11

I noticed the Finder uses this kind of sorting, so I was wondering if there's already a way to do it, or if I will have to make my own compare: method which will sort the objects like this. Thanks in advance.

Nevermind, found my answer here: http://developer.apple.com/qa/qa2004/qa1159.html and here: FilenamesArentStrings

