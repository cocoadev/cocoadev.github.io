---
layout: page
title: FlexibleDesign
---



In almost all software, the requirements are constantly changing and it is therefore important that the design be flexible to those changes.

Applying OnceAndOnlyOnce can greatly increase the flexibility of the design because, if something changes, it only needs to be changed in one location. Encapsulation also plays an important role in a FlexibleDesign because a change is less likely to affect other areas of the system.


**Taking It Too Far**

FlexibleDesign is often taken too far - adding abstraction to make way for possible future requirements. These requirements may never come or may come in a different form. The result is an overly complex design. One way to avoid this is to only add flexibility if it will be used by the current requirements. If a new feature is needed in an inflexible area, make it flexible before adding the feature. This way, it will be flexible to future changes in that area.

