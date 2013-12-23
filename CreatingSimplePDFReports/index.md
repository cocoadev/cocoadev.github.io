---
layout: page
title: CreatingSimplePDFReports
---

I am writing a Cocoa/Java application for managing financial information, and need to generate reports.  The reports are fairly straightforward, for instance similar to a standard brokerage statement, but I am trying to figure out the best tools to use to generate the reports.

ReportMill probably does what I need, but is way beyond my budget and does way more than I need.  I have tried creating an NSTextView in InterfaceBuilder and programmatically filling it with formatted text, and then printing and/or creating a PDF from there.  However, I need some formatting and pagination behavior that is beyond the scope of a simple NSTextView, for example a header that repeats at the top of each page, and table headers that repeat after each page break.

I could probably do something with for instance Excel and ODBC, or even MS Word, though I'd much rather control it from code within the Cocoa app, so I'd rather have a developer tool.  I am hoping that this is a common problem and there is either a product or some open source software or example code that can do what I need.

Thanks in advance for any tips or suggestions!

Randy

----

Well, if your solution is Tiger or above, you can use PDFKit ...

