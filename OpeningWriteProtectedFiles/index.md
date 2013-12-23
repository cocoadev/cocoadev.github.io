---
layout: page
title: OpeningWriteProtectedFiles
---

I am writing an app that allows (among other things) the editing of write protected files (similar to TextWrangler or sudo pico myFile in the Terminal). So I started out with a simple texteditor. I can browse to the directory where the files are, but they are all greyed out. To make them editable, I overwrote openDocument in MyDocument, and added the filetype of the files I am interested in. Indeed now the files are clickable in the OpenDialog and I can open them. Then when the user wants to save the file, I need to put up a dialog using the authorization API where the user enters name + password.

My question is, if this is the right approach (using openDocument), or are there other possibilities that I may have overlooked?

