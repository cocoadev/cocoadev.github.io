---
layout: page
title: CFXMLTreeInternalDTD
---

Is it possible that the Core Foundation XML Parser, namely CFXMLTree, chokes on internal DTDs?

I had my XML validated with XMLLint, and Safari displays it as good as it can. Any ideas?

Thanks,

Alex

----

It's possible - it should just be skipping it. Can you point to a specific XML file it does this with or is it anythign with an internal DTD?

- ChrisParker

