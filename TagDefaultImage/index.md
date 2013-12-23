---
layout: page
title: TagDefaultImage
---

With the release of v1.1 of our first iPhone application we encountered the following problem: where to put a user readable version number without introducing some kind of �splash screen�?

One solution for this is, to put a text with the version number on the �Default.png�. This is an image which is displayed immediately when launching an iPhone app until the application is fully loaded and initialized. Usually this image is a �neutral� screenshot of the application itself.

The problem with this solution is, that the developer has to edit the Default.png each time in a grafic editor to set a new version number. This gets even worse, when the application uses mutliple localized Default.png files.

To solve this issue, I wrote a small command line tool, which takes a �blank� Default.png, writes some text on in (usually the version number) and writes the result back to disk. This way this process can be easily integrated into the build process of the application. The only thing which has to be changed between releases is the version number itself, which may be a build setting.

For more information on this tool and a link to the free download, see our blog at http://pleasantsoftware.com/blog/?p=57

-- 10/01/08 Eberhard Rensch/Pleasant Software
