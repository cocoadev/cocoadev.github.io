---
layout: page
title: DocumentBasedApplicationsThatAttemptToOpenAllFiles
---




If you would like to have a document based application attempt to open any file thrown at it, do the following.

Add a document type in the properties tab of the target info for the document based application and do the following:


*Name the type "AnyType"
*Enter "*" for Extensions (minus quotes)
*Enter "****'" for "OS Types" (minus quotes)
*Enter the document class name that will attempt to open the file (e.g. MyDocument)


Now all you have to do is override     initWithContentsOfFile:ofType: and drop any file on the dock to test it out!!

*note: initWithContentsOfFile:ofType: can pass a directory, so don't expect AppKit to do anything for you when you allow all file types*

--zootbobbalu

