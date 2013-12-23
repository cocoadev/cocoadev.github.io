---
layout: page
title: ReadWriteRTFCorrectly
---

Cocoa reads and write RTF - but ignores the writing direction. 

For example, you can set writing direction in a NSMutableParagraphStyle:

    
[paraStyle setBaseWritingDirection:NSWritingDirectionRightToLeft];


Then you can add this paragraph style to a NSTextStorage. But it will not save this attribute when you write RTF from it. 

Anybody knows of open source/gnu **good** RTF code?

----

You might check GnuStep. -- RobRix

