---
layout: page
title: SolutionToNSSearchFieldWrapping
---

I have a pasteboard type that contains \r characters so that I can cut and paste rows from a table view.

If I inadvertently paste the contents of that pasteboard type into a search field that I have on the same window, things were getting
ugly in a hurry. The text wrapped over several lines and drew stuff outside the bounds of the search field cell.

Even after I canceled the search, the wayward drawing artifacts were left over on my window. Unacceptable.

The search field is added programmatically to the toolbar for the main window.
Thereupon I have set its maximum and minimum size using the setSize: method of NSToolbarItem.

When I pasted the contents of the same pasteboard into the search field of Xcode or Safari, for example, it did not wrap
and scrolls across nicely.

----

In my original post SearchFieldWrapsTextUgly, one respondent asked me a question I could not answer coherently

*Why are you using \r characters to cut and paste rows from a table view? Wouldn't it be easier to just use your own pasteboard type, instead of the text-specific one?*

To which I replied:

**My pasteboard methods, including my custom pasteboard type, return NSString objects containing the offending \r characters. This is not the problem. Those strings returned by my pasteboard methods paste just fine into the Search Fields supplied by
apps such as Xcode and Safari. What I am inquiring about is how to condition my Search field to accept such strings of characters.

I have tried lots of other things besides \r or similar codes. If I don't use it, the table does not properly accept pasting of table view rows
when cut-and-paste is used internally to the app.**

Then my interlocutor replied:
*Was \n one of the similar codes you tried? Or just remove the \r's from the strings.*

I rejoined:

**Yes I tried \n. It did not result in proper pasting into the app's table view internally.
To elaborate: this character is the
record separator in the paste operation to the table. Think about the "componentsSeparatedByString" method of NSString.
**

----

The answer (of course) was to write an NSFormatter subclass for the search field that removed the \r characters and replaced them by spaces,
and sending a setWraps: NO and setScrolling: YES to the search field at the time I assign it its formatter.
But <BOO HOO> none of you fine folks saw fit to point me in that direction. Cost me 24 hours <SNIF> which I put to good use in woolgathering.

Note that the default behavior of NSTextField does not cause it to go ballistic when you paste a multi-line string.

*It's not NSSearchField's default behavior either, which I very briefly verified using Interface Builder. You must have been doing something strange.*

----

Something strange? You mean, like instantiating a search field programmatically? Doesn't seem all that strange to me (how else do you get one into a toolbar?) But no, I guess I was not using the default behavior, since I also had to set wrapping and scrolling programmatically. Funny thing was, without the formatter, these settings did not prevent my unformatted search field from giving me grief. I therefore conclude that the missing piece of the puzzle was indeed the formatter.

*When you instantiate a control in code, you have to set a lot of thing programmatically. Maybe you missed one. Does it (not) work the same way when you use one from IB?*

Interesting. After installing the formatter, I only had to set the wrapping and scrolling programmatically. What else might there be that is undreamt of in my philosophy?

*I don't know. Why don't you drag an NSSearchField into a custom view in IB and find out?*

Because that wouldn't adequately explain why a search field instantiated in code does not behave the same way but only provide another somewhat Rubegoldbergian way to put the search field into the toolbar in IB while being (potentially) less Rubegoldbergian in not forcing one to write the formatter. Sounds like another case of "you pays yer pittance and picks yer poison".

----

How is putting your search field in the nib "Rubegoldbergian"? You stick it in a separate view that nobody ever sees, hook up your outlet, and bam. No code to write to create it, it works correctly, and your program gets simpler.

**hrm. so it does**

*It's not, really. I apologize profusely for being obstinate. Most other examples I have seen of installing controls with subviews into a toolbar use exactly the technique you describe. I am an experimentalist, which is hazardous in an activity such as this. Your point is well taken. Me, I am just learning how to use these friggin frameworks, and I was glad to learn how to write a formatter.*

No problem. If you're still wondering what the difference is, create a search field in the nib and print out all of its properties, then do the same for the one you created in code. The differences should be obvious. once you do that. If you try that, please let us know what you find.

**
Of course, I did not have to print out ALL its properties...The differences were obvious.
I found, to no one's great surprise, that by default, the search field instantiated programmatically returns YES and the one from the IB Palette returns NO when you send it the message [ [ searchField cell ] wraps ]. I guess that about "wraps" it up. <snrk> <snrk> 
**

**[note1]**
A definition of Rubegoldbergian. http://www.m-w.com/cgi-bin/dictionary?va=Rubegoldbergian

----

Would I be correct in my understanding that instantiating the search field in code might allow one to test whether the app
was being run on Panther (or later) or on Jaguar, and instantiate a normal text field in the latter case, for backward compatibility?

*You could also just have 2 fields in the nib, and use either one as appropriate.*

