---
layout: page
title: ApplyingStylesToWebView
---

Is there an easy way to apply a CSS style to a page in a WebView?

Thanks.

----

I just insert this into the <head> tag:

    
<link REL="STYLESHEET" TYPE="text/css" HREF="style.css">


There should be a better way though...

--ZacWhite

----

Sorry, I should have been more clear. I meant, is there a way to do it without editing the actual page, like the Style Sheet option in the Advanced tab of Safari's preferences?

----
Read the documentation. There's a method for setting a user-defined stylesheet.

----

Thanks -- found it.

