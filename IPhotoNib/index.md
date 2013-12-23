---
layout: page
title: IPhotoNib
---

I'm playing around with creating custom themes in iPhoto for the book feature of the app.  If you did down into the proj file you find a Books folder, and if you dig down further you find a nib file for each page style for each theme.  The problem I'm having is that the changes I make to the nib file aren't showing up in the Application.  However, if I remove the nib file, I no longer have the page template available.  Is there some other step I need to do other than saving, like compiling the nib?  

I solved my own problem.  iPhoto does some processing and caches .ppd files for each page template in     ~/Caches/iPhoto Cache.  If you delete those, your changes will show up.

