---
layout: page
title: NSTreeControllerAndMultipleEntities
---



I have a set of entities:
    
 Range
     name   - String
 Area
     name   - String
 Language
     name   - String
     descriptiveText  - String

I would like to display these on the same level of an NSOutlineView. So, it would display something like
    
  Ranges
     name
     name
  Areas
     name
     name
  Languages
     name
     name
  Other
     Thing1
     Thing2

The Other list is a non Core Data list. Taking a look around it seems like the best way to do this is with an NSTreeController but I'm not sure how to setup the controller to use multiple entities, and non-entity items, at one time.

Are there any documents or blogs that discuss how to tackle this?

