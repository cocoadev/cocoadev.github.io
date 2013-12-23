---
layout: page
title: FancyAboutAndNSTextView
---


I am trying to apply the animated scrolling of text from Apple's FancyAbout sample code example to a window with an NSTextView object and a button to instigate the scrolling.

The example code says, "To keep things simple, this application just puts the text inside an NSScrollView, hides the scroller, and uses a timer to regularly advance the amount by which the text is scrolled". I am trying to do a simpler example, using Interface Builder to create a window with two items, an NSTextView and an NSButton. I placed some boilerplate text in the NSTextView that is longer than the view, so that it has a vertical scroller.

But I am confused about the difference between an NSTextView and an NSScrollView, and how to access the NSScrollView in Interface Builder and Xcode, so I can use the code given in the FancyAbout sample to do what I want. Is there an NSScrollView automatically created when I caused the vertical scroller to be shown in Interface Builder? When I click on the NSTextView item in the Window, the Inspector shows NSTextViewInspector and the popup says Attributes. If I change the popup to "Custom Class", it shows Class NSScrollView, but I see no way to add an outlet for NSScrollView, which is the class used by the example code. Must I use the Layout command "Make Subviews of .. ScrollView" in order to add the outlet? But then there are two sets of scroll bars. Sorry I am so dense.

Larry

--------------

I found the answer in the CocoaBuilder archives - the method "enclosingScrollerView" returns the desired NSScrollView.

Thanks,

Larry

