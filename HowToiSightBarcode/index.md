---
layout: page
title: HowToiSightBarcode
---

Is there a framework for iSight barcode scanning, or can anyone explain how it's done?

Thanks.

----

Hm, I somehow doubt that there is an open framework. That is very hard to accomplish but I guess any kind of barcode recognition code could be integrated for use with the iSight. Just take specific frames from the iSight and run them through the recognition engine. Here are some engines from SourceForge. You could possibly create a binary from this source and then use NSTask and some other crap to process the images you capture from your iSight. Most of this is Java but I think one is Active-X...which you probably can't use.

http://sourceforge.net/projects/jbarcode-scan/
http://sourceforge.net/projects/barbara/ (This is the ActiveX one.)
http://sourceforge.net/projects/zeitkarte/
http://sourceforge.net/projects/barcodereader/

- ZacWhite

----

Try out the open source barcode reader for Cocoa. http://www.bruji.com/cocoa/barcode.html  Quite simple to integrate into a Cocoa project, just drop the MyBarcodeScanner.h/.m into your project and use the sample code in the Controller file to call the function that returns a barcode.  It an algorithm that reads UPC and EAN barcodes at the moment and is based on the Apple quicktime image capture to get the frames from the iSight.

----

Thank you very much!

