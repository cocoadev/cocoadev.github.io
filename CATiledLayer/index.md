---
layout: page
title: CATiledLayer
---

CATiledLayer is used when attempting to represent a layer (Apple thinks this is likely to be an image, but it might be something else) which is "too large" (seemingly exceeding 2k x 2k or so).  

When using CATiledLayer, make sure that the size of the layer is whole decimals.   It appears that any value after the decimal point will cause a setContent: call to be made for each draw operation and that all tiles that are partially or fully visible will be redrawn whenever a rectangle is invalidated for the layer.

