---
layout: page
title: NSTextStorageChangesWithBindings
---



I have a NIB with an NSObjectController containing a "Journal Entry" object model. The model has a -contents key that accepts NSData for RTF data. This is bound to an NSTextView's data binding with 'continuously updates value' checked for various reasons (not the least of which is to immediately mark the entry as edited). Everything here works just as you'd expect.

The problem is when I edit the NSTextView's NSTextStorage object to change attributes. Namely, I've created a hyperlink panel to allow the user to select text and hyperlink it to any given URL. This part works wonderfully. However, this change is never observed, unless you edit the text via normal means (ie, typing into the box) after the link attribute is added, triggering the change.

I have tried forcing it via the following (where "journalEntry" represents the model object):

    
- (void)assignLink:(NSURL *)linkURL
{
     NSTextStorage * textStorage = [textView textStorage];

     [journalEntry willChangeValueForKey:@"contents"];
     [textStorage beginEditing];
     [textStorage addAttribute:NSLinkAttributeName 
          value: linkURL range:[textView selectedRange]];
     [textStorage endEditing];
     [journalEntry didChangeValueForKey:@"contents"];
}


I've also tried [entryController will/didChangeValueForKey:@"contents"]; on the actual controller that references journalEntry as its content object. No joy. It doesn't commit the change. So .... how do I do this? I *did* "RTFM", but maybe this is a simple case of my not knowing "WTF" to look for in "TFM". ;-) If any further information is needed or I need to clarify anything, please let me know; I'd be happy to.

----

*In the case of Bindings, TFM is practically nonexistent.*

Indeed. I've noticed that. ;-) It seems to me there should be *something, somewhere* explaining how to trigger the NSTextView's binding mechanism  ... alas, no. (edited for clarity)

----

In your code above, you're changing     textView's data, but you're sending a notification that says you're changing     journalEntry's data. Shouldn't you be sending the     willChangeValueForKey: etc. to the     textView instead?

*Let me rephrase this: My model isn't observing the text view, the text view is observing my model. So it seems that triggering KVO on the model's "contents" may be wiping out the changes to the text view's text storage. So the problem is, I need to change the text storage, then trigger the NSTextView's bindings mechanism (whatever that actually is) that makes it dump its contents to whatever it's bound to.*

I don't see why that matters. You're changing the text view's contents, and you want to trigger an action because of that change, so it seems to me that you should be sending a notification to the effect that the text view has changed.

----

*Actually ... I feel like a moron. -didEndEditing does trigger the right response. So, the lesson is: If you manually change the contents of a text view whose 'data' or 'value' is bound to some model, just call [textView didEndEditing] and all is well. (sigh) It IS in the manual, it's just not immediately apparent what you're supposed to do in this case.*

Better:
    
// Add the URL attribute to selected text
[textStorage beginEditing];
[textStorage addAttribute:NSLinkAttributeName 
     value:linkURL range:[textView selectedRange]];
[textStorage endEditing];

// Notify textView, wrapping in KVO to be nice
[journalEntry willChangeValueForKey:@"contents"];
[textView didChangeText];
[journalEntry didChangeValueForKey:@"contents"];

