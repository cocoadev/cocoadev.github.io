---
layout: page
title: SubclassingNSArrayController
---

Hi,

When subclassing General/NSArrayController (or probably any object controller) what member should I override to do additional init style processing.
I'm currently using awakeFromNib but that doesn't seem like the best solution (if I programatically creating the controller it won't be called).

I've tried -(id)init and - (id)initWithContent:(id)content but neither is called.


Any ideas?

----
When non-view, non-custom objects (or their subclasses) are unarchived from a nib file, the method     initWithCoder: is called, rather than a normal initializer. Probably your best bet is to override     initWithCoder: and     initWithContent:. IMHO, overriding     awakeFromNib, while workable, wouldn't be quite as good as     initWithCoder:, considering both the original purpose of     awakeFromNib (for controllers to modify their outlets) and the fact that few people ever call     super when implementing     awakeFromNib. --General/JediKnil

----
Thank you. That certainly sounds like the way to go. Cheers.
