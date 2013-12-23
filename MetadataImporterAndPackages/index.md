---
layout: page
title: MetadataImporterAndPackages
---



So, I have an application that saves a project in package format. The saved package consists of multiple RTF files and a single project data file. All that's working just fine. The trouble starts in the very beginning - the conceptual level. ;-) I'm trying to create a metadata importer that, when indexed by spotlight, the *content* of the RTF files can be searched, but is represented by the package itself. In other words, if I have the word 'foo' in one or more of the RTF documents in the package, I want the package itself to show up in a spotlight search. It's also worth mentioning that I'm doing it this way, rather than storing the RTF data all in the same file, because there could be many LARGE RTF files in any given 'project' package.

Can someone give me a run-down of what I'd have to do differently from a standard file / metadata importer to make this magic happen?

----

6/13/05 - Bumped this topic again; I'd like an answer to this as well, please!

Apple Sample code has an example of this:

http://developer.apple.com/samplecode/Spotlight/Spotlight.html

EnglaBenny

