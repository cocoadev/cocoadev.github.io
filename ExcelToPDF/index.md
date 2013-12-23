---
layout: page
title: ExcelToPDF
---

Is there a way to extract properties of Excel images (text, tables, charts), make changes and convert to pdf using Cocoa and/or AppleScript?  Thanks.

----

You can access an open Excel document through AppleScript. Use Script Editor to view Excel's dictionary, or search sf.net for "excel" to download several libraries that can read/write Excel files. As far as rendering to a PDF, you may be able to automate that through UI scripting, but for simple spreadsheets it would be simplest to render it yourself using an Excel library.

