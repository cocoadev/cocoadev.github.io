---
layout: page
title: SourceListOutlineViewBindings
---

I am having a similar problem to that of the person in NSOutlineViewBindingsHelp. I put it on a separate page because it seemed sufficiently different.

Basically, I'm trying to implement the usual iTunes/Mail.app source list style interface.

It should have the following structure:
    
Home
Groups
|->Group1
|->Group2
|->Group3 etc


Where the Home and Groups root nodes are predefined, and the subgroups (Groups1 through 3) are fetched from a core data store. The subgroups are defined in an entity "Group".

I've managed to make this work up to a point, but it's using *really* nasty code (no bindings), and it's nigh on impossible to remove groups from the store. I'd ideally like to scrap it and start from scratch using bindings if possible. Can anyone suggest how best to implement this? It's really starting to bug me now.

Thanks for any suggestions. B.

