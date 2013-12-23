---
layout: page
title: OnlyPDFAnnotationTextSupportsClicking
---



I am working with PDFKit, using PDFAnnotations, to, well, annotate my PDFs.

However, it appears that only PDFAnnotationText sends the PDFViewAnnotationHitNotification notification.

This is a real pain, because I want to use PDFAnnotationSquare, but this appears not to send the 'hit' notification. 

So how can I tell when the user clicks my annotations?

Thanks thanks and thanks.

