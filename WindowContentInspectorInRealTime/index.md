---
layout: page
title: WindowContentInspectorInRealTime
---

Let me know if I'm going down the wrong path here, as I'm a Cocoa/General/ObjC newbie.

I was examining Apple's General/UIElement Inspector which fascinated me.  What it is is a little window in which the identities and coordinates of the UI elements (like buttons and scroll thingies) of any window on your desktop come spewing out in real time as you move your mouse around those windows.  And apparently it uses the General/NSAccessibility class.  But when it comes to Content in the window, it just says:  "Content" and gives an Array size

So I went into the application's source and went to the parts where Content was referred to, and changed "Array size" to "self", to see if text Content would spew out if I put my cursor over a Safari webpage (after rebuilding and running the my altered version of General/UIElement Inspector).  But of course that led to an all-out Crash.  If it were possible to extract text Content from a second window in real time, I guess I would have had to also worry about allocating memory for the Content, which I didn't do.  But in any event, my question is, is this theoretically possible to do?  Or is text content flat-out internal to the window itself?

On further thought, I wondered if I constrained the text Content to just come out word by word, if that would help any memory allocation question.  Or is this inquiry an exercise in futility.  Can anyone enlighten me?  Thanks.


 ----

Hmm. It's a little hard for me to tell what exactly you want to do or what the question is.. But here are some random answers, you can see if they correspond to your questions.  Is it possible to get the text being displayed in some view in a random application?  Yes.  Is it simple?  Not really. :)  I can give you some pointers for places to start though.  You're not so interested in General/NSAccessibility, that's for developers who want their custom interface elements to be made available to apps like General/UIElement Inspector - such an app is called an Assistive application.  You should read here: http://developer.apple.com/documentation/General/ReleaseNotes/Accessibility/General/AssistiveAPI.html

That said, if you're truly new to cocoa/objective-c.. um, I don't think this is a good place to start. :)  If what you're interested in is the result, and not the process, then  you might find it a little easier to use applescript, which puts its own interface on the General/AssistiveAPI: http://www.apple.com/applescript/GUI/ .  Maybe.


 ----

Cool -- just learned something.  Thanks for pointing me in the right direction.  I will look into the areas you suggested.
