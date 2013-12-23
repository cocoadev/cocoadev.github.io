---
layout: page
title: ReturningValues
---

Hi!
This is a question and any help would be great
Let me explain what I want to do
I have
 1. a custom class MyCustomClass
 2. a Cocoa document class which I call MyDocument
 3. a Custom View that I call MyView

MyCustomClass looks like this

     @interface MyCustomClass : NSObject {
  float a;
}
//OLD -(float)Returna; // Obvioulsy it returns a 
//update : -(float)ReturnA; // Obvioulsy it returns a 


@end


MyDocument.h looks like 

     @interface MyDocument : NSDocument {
 MyCustomClass  myClass;
}
-(float)returnA;  // this method here return [myClass returnFloat];
@end


MyView.h looks like this

     @interface MyView : NSView {
 IBOutlet id *doc    // I then linked this outlet via IB to the MyDocument (First responder)
}

The problem that I encounter is that I cannot use the method [doc returnA] from myView. it looks like at the compilation, the Objc Compiler don't find the instance method , then don't allocate a return value placeholder.

Is anybody knows of a solution ?

Thank you

--Francois

----

Must we continue to use "My" everywhere? -- MikeTrent 

Agreed.  "my" pretty pointless, and pretty juvenile.  // should I use "your" ? --Francois

Disagree. "my" is a namespace!

----

I hope this isn't your real code, since [doc returnA] is doing a [myClass returnFloat] but you only have a Returna in myCustomClass (wrong name, and your capitalization conventions are inconsistent).  Assuming you're invoking [doc returnA] and that method actually exists, that should work.  You'll get a compiler warning since you declared "doc" in your view as an id, rather than a MyDocument.  Stick in a @class MyDocument;  and change your "id *doc" to "MyDocument *doc", and hopefully it should work.

**
*For the records, id is already a pointer, so id* is a pointer to an object pointer.*
**


----

I made a capitalization mistake writing this code on the discussion forum.
The Compiler returned an error (and not a warning ) since the "id * is a pointer to an object pointer". Thanks for your comment. 
Using "@class MyDocument MyDocument *doc" produce the exact same error ( this is why I asked for Help )

I solved this by doing the following

1. MyDocument creates the object of type MyCustomClass which I called MyClass
2. I declared a new object docView in MyDocument and it sends the message [docView setDoc:MyClass] which obvioulsy sets doc to MyClass
3. Now I can directly call the instance methods of MyClass without having to have MyDocument as a proxy.

Conclusion : This new code looks definitively more like ObjC.

Thanks to all. -- Francois


@class MyDocument MyDocument *doc should produce an error, since it's bad syntax.  Put @class MyDocument; somewhere at the start of the file, then in your object structure, have MyDocument *whatever.

----

I will agree "my" is a namespace, but one that should never be used. to elaborate, names like "MyDocument" and "ReturnA" are useless because they're made without context. Of course you would never use them in real code (right?) but it begs the question why should we pollute our learning materials with examples that shouldn't be used? Why not define some context, such as "TextDocument" and "- (id)text;" and let the reader figure out how the example relates in their own situation? I.e., each by real-world example, not teach by FillInTheBlankProgramming. -- MikeTrent

