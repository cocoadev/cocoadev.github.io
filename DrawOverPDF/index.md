---
layout: page
title: DrawOverPDF
---



I am wanting to develop an application that makes it easy for users to fill out a form, which is represented by a PDF. I am wanting to place controls (text fields, checkboxes, etc) over parts on the form where you would normally write. Would it be best to use a PDFView and add subviews to it, or just draw the PDF as a normal image in my own NSView that handles everything? I want it to look like the user is typing directly onto the form.

I'd also like to add that it would be nice to support zoom in/out, so I'm thinking a PDFVIew sublcass may be best.

----

It's going to be hard to make subviews line up and scroll correctly. Why not just use an HTML form?

----

Um, that's not an option. The form is a PDF file. 

----

The only hard thing about lining up is going to be finding the coordinates of the form elements. I don't know if the PDF API provides for this or not; if it does, this will be easy. Scrolling correctly won't be a problem as long as the elements are a subview of the PDFView.

----

Well I can't believe I overlooked annotations. That seems like the best way. I will just subclass PDFAnnotation (or one of the provided ones) and add my text view to that. Plus they handle scaling automagically!

