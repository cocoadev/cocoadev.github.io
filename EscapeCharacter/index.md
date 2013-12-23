---
layout: page
title: EscapeCharacter
---

There is NSCarriageReturnCharacter, there is NSUpArrowFunctionKey but you won't find an according key for the Escape key on your keyboard.

But after some searching on Google I found out that it's 27. So if you want to catch the escape key being pressed in your view, do this:

    
- (void)keyDown:(NSEvent *)theEvent
{
    unichar key = theEvent charactersIgnoringModifiers] characterAtIndex:0];
    if (key == 27)
    {
        [[NSLog(@"Escape Key Pressed");
    }
}


Hope this helps. Tested it on my PPC and it worked like expected.

Regards,

--MatthiasGansrigler

----

Or you can just use '\e'.

----
D'oh!

----
Depending why you are trying to detect the escape key, you are probably better off using NSResponder's cancelOperation: method
----
What would be the appropriate key for the Del key (Note: not the backspace key, but the del key) on the keyboard? NSDeleteCharacter doesn't work for me...

----

Try NSDeleteFunctionKey.

