---
layout: page
title: ImageInNSTableHeaderCell
---

I was wondering how I would put an image in a NSTableHeaderCell. Thanks.
----
Never mind, I found it out. I'll share.
    
NSTableColumn *priorityColumn = [fileView tableColumnWithIdentifier:@"filePriority"];
NSTableHeaderCell *priorityHeader = [[NSTableHeaderCell alloc] initImageCell: [NSImage imageNamed:@"priority_header"]];
[priorityColumn setHeaderCell:priorityHeader];
[priorityHeader release];
NSImageCell *priorityImageCell = [[NSImageCell alloc] init];
[priorityColumn setDataCell:priorityImageCell];
[priorityImageCell release];

This helped me, I hope it helps you people too.

--JoshaChapmanDodson

