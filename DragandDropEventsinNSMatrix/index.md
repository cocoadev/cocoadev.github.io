---
layout: page
title: DragandDropEventsinNSMatrix
---

Hi,
I have an application where i am creating a NSMatrix of NSImageCells to display thumbnail images. In this matrix i am trying to support Drag and Drop.
If i create the NSMatrix in Interface builder, drag and drop is working fine. But for the same code, if i create the NSMatrix dynamically, i am getting only mouseDragged, mouseDown and mouseUp events. I am not getting draggingUpdated, draggingEntered, draggingExited, performDragOperation events.
Because of this i am able to just drag the image and i am not able to do drop...

I am creating the NSMatrix dynamically like this:

pThumbnailMatrix = [[DragMatrix alloc] initWithFrame:frame
                                                           mode:NSListModeMatrix
                                                           prototype:NSImageCell
                                                           numberOfRows:2
                                                           numberOfColumns:5];

[pThumbnailMatrix setCellSize:NSMakeSize(100.0f,100.0f)];
[pThumbnailMatrix setIntercellSpacing:NSMakeSize(10.0f,10.0f)];

Here i have subclassed NSMatrix to DragMatrix to perform Drag and Drop.

Can anyone please help me why i am not getting drag and drop related events only if i create matrix dynamically during run tyme? Am i missing anything here?
----You'll need to post more of your code for us to see why drag and drop isn't working. You've posted nothing that tells us this.

