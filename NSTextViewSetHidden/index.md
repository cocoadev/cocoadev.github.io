---
layout: page
title: NSTextViewSetHidden
---



Hi there,

I'm trying to set a NSTextView's setHidden status. The only sure-fire way I can get it to hide is to set it from Interface Builder (but then I can't seem to set it to display when I want to so setHidden: NO doesn't make it appear). I've tried setHidden: YES in the awakeFromNib area but that doesn't do it either. I've added setNeedsDisplay:YES and that doesn't seem to change it either. Suggestions?

----
send -setNeedsDisplay:YES to the hidden view's superview when the view his hidden or unhidden.  

[someView setHidden:YES];
someView superview] setNeedsDisplay:YES];

And please don't abuse this view hiding feature.  As a general rule, user interface elements should not appear and disappear without using an established GUI convention like tab views...

----

And who are you, the interface police? There are perfectly legitimate reasons for hiding a text view, for example [[HandlingAccentedCharacters. But even if, stop with the sanctimonious whining about what people should or shouldn't do. It's not the law, and it's their project! If the result sucks, the market will decide.

----
With that attitude, why give people advice at all?

If I see somebody doing something potentially stupid, I will inform them of it. This goes for an obvious memory leak, a bit of code that won't work with non-English scripts, or a dumb bit of GUI design.

----
I can't see where HandlingAccentedCharacters has anything to do with a hidden text view.

----
See the discussion at the end about using the FieldEditor to capture the incoming text but hiding it so it doesn't interfere with your control. The FieldEditor is an NSTextView.

----

There's a big difference between stopping someone doing something "potentially stupid", like a memory leak (though in fact this is not usually stupidity rather than not quite having grasped the concepts involved, which I don't consider "stupid"), and quoting chapter and verse from the HIG, or your interpretation of it, like it's some holy writ. The original question provided no context at all, so you're just making assumptions; your warning simply comes off sounding sanctimonious. It's not like you personally will have to suffer the consequences if the result is a UI disaster. Most people are capable of reading the HIG for themselves and reaching their own conclusions. Most people are not dumb, and don't need you to save themselves from their own decisions. For apps that reach the market in that state, (very, very few, though there are some) users will vote with their trash cans.

Where code is posted containing an obvious mistake, then yes, I think that gently pointing it out is likely to be a good idea. But read what you wrote - you just sound like an arse.

----
But this *is* an obvious mistake.

People often think that because they spent sixteen hours a day using their computer and experiencing all the various GUIs on it, they are qualified to make great GUIs. It's not true. Designing a good interface is *hard*, and there are a lot of rules and guidelines on how to do it that are not obvious.

There are a lot of people out there who aren't aware of some basic rules, like "view should not hide and show without very good reason". This is not a fault, it's just ignorance, like somebody using     +stringWithCString: because they don't realize how badly it will fail with non-English text. Now, why should I point out the latter but not the former?

Yes, there are legitimate reasons to do this. It's also likely that the poster was simply creating bad design. If he wasn't, then he can pass over the advice and move on. If he was, then hopefully he will take it to heart and understand why it's bad design.

I don't see how you could possibly think that the advice poster sounds like an arse. He was polite, gentle, and even went to the trouble of answering the question first. Honestly, if you are somehow offended by this unthreatening action, then you need to calm down a bit and realize that not everybody is out to get you.

----
Thanks for the advice - though... I went to the superview to change the setNeedsDisplay and still wasn't able to make it appear/disappear. I'm the orig. poster and I was building my own little debugging utility. It's more for private use (in fact I never intend on releasing it for public consumption) and I thought I could try different things. I understand how this can be bad interface when UI elements disappear but it does happen quite often. Context sensitive menu options for example change based on things. I don't consider myself to be a GUI expert but it definitely is interesting to see what can be done.

----

You probably need to do the     setHidden: thing on your text view's     enclosingScrollView.

----
I had the same problem and     setHidden: on the text view's     enclosingScrollView does indeed fix it - thank you previous poster!

----
I've the following code

    
NSView* pane = ...;
[pane setHidden:YES];
pane superview] setNeedsDisplay:YES];

    pane is the outlet connected to [[NSTableView descendant in NIB file. With that code I supposed to hide the view. But unfortunately it didn't work. I'm quite new to Cocoa. As far as I understand, this approach, i.e. hiding/showing views is considered to be a "bad practice". Nevertheless, what am I doing wrong???

----
Will answer myself :)
    NSTableView is contained in     NSClipView which is in it's turn contained in     NSScrollView. So, the code should look like this
    
NSView* pane = ...;
[pane superview] superview] setHidden:YES];


----
Or much simpler:     [[pane enclosingScrollView] setHidden:YES]

----
Perfect! Thanks a lot. 
I came to [[XCode/Cocoa from .NET, so, for me this framework seems VERY different from what I used before. Completely different approach. Feels myself like in kindergarden :)

