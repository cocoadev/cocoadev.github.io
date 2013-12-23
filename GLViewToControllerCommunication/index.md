---
layout: page
title: GLViewToControllerCommunication
---



I have created my own GLview and am happily displaying simple primitives.  I've also implemented a handy camera management scheme.  What I'd like to do is report the various numbers that describe the camera using NSTextfields.

The view is implemented within GLView.mm/h, the OpenGl is all done within OGLManager.cpp/h.  There are also a contoller, GLController.mm/h and a model TriNrml.ccp/h. 

The camera is updated anytime the user Option+drags within the view.  My thinking was to create an action within my Controller that would be called anytime the user performed this operation.  I imagined that this would be a simple case of ctrl-dragging from the view to the controller instance within IB.  However, when I do this I don't see the Controller's actions within the property pane.

What am I doing wrong here?

----

You need to do this programmatically. Create an outlet in your GLView class to the Controller. Whenever your camera is updated (it looks like it might be in the mouseDragged: method), send the Controller the message to update the text fields.

