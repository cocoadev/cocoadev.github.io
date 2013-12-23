---
layout: page
title: WebCoreMemory
---

Does anyone have a rough idea of what should be happening in memory when you load different pages in a General/WebView, consiting of nothing but an img tag and an appropriate img.  Does General/WebCore maintain history?  And if not, which if I recall, it doesn't, why is memory usage going up with each consecutive load -- objectAlloc reports that all additional  memory is being allocated from within web ruitines...

I think by default General/WebKit does indeed store back-forward lists. You can prevent it from doing so by doing -General/[WebView setMaintainsBackForwardList:NO]; --General/AdamAtlas
