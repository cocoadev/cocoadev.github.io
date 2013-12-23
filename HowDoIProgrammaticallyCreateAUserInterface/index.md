---
layout: page
title: HowDoIProgrammaticallyCreateAUserInterface
---

**Question: How do I programmatically create a user interface ?  Specifically, how do I create a radio button matrix ?**

**Answer:**
Thanks to Stepwise for hosting this sample.

http://graphics.stepwise.com/EMB/RadioSample.zip

I just wrote a little sample with commented source code in an 
Xcode project.

There are countless little samples out there already.  Here is one more 
with my spin on the techniques.

This sample creates four radio button matrices using four different
techniques:

1) Just do it in Interface Builder with no code

2) Copy and reposition the matrix previously configured in IB

3) Configure a custom view previously positioned in IB

4) Build up the matrix from scratch

**Note:** There is almost never a reason to use techniques 3 and 4.

**Note:**  Technique 2 is used by IB whenever entering "Run" mode and a similar technique is used when any nib is loaded in any application.

**Note:**  There is virtually nothing that IB can do that you can't do in code because every user interaction within IB ends up calling code similar to the code in techniques 3 and 4.  IB executes the code to configure a "live" object.  When you save a nib file, the live objects with all of their configuration (state) are archived. When a nib is loaded, the objects are unarchived and come alive again. IB exists so that you don't have to write or maintain all the code in techniques 3 and 4.  At least use technique 2 in favor of techniques 3 and 4.

