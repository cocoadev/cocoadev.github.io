---
layout: page
title: NextKeyViewDoesntWork
---

I have an NSDrawer with a ton of text fields (or, I suppose more properly, a NSDrawer's content view), organized in two columns. I've tried to set the tab order to go down each column by connecting each NSTextField via its nextKeyView outlet, but it's ignoring this tab order and going between columns with each tab stroke. Does nextKeyView not work in NSDrawer or something?

----

What happens if you programmatically set things up by calling     setNextKeyView:(NSView *)aView on the text fields in the drawer?

----

Nothing, unfortunately. I just tried it with a couple of the NSTextField instances via their IB outlets, attempting to make a closed loop tab order where one would tab to the other. The app ignored me, though I get no errors in the run console. It still always tabs across columns, rather than down them. Am I missing something obvious? It should be as easy as connecting the nextKeyView outlets in IB or doing it programatically, right? No setting a flag somewhere to accept tab order or tab events?

----

I also have seen similar problems.  I'm wondering if it's related to the way I'm hi-lighting the first text field in the view?  There seems to be a number of ways to do this.  Which is the correct magic incantation?

textField window] makeFirstResponder: textField];

----

[[textField window] makeFirstResponder: textField];

is correct.     selectText: is for selecting the field's text, not necessarily making it first responder, and the docs for     becomeFirstResponder explicitly say never to call it directly, to use     makeFirstResponder: instead.

----

Make sure that the window on which the drawer is opening has its     initialFirstResponder outlet set, and a set tab order. The keyboard loop mechanism seems to want that.

----

For further hints see [[KeyViewLoopGuidelines

----

**Button in Drawer not responding to Keystrokes**

Nor, I should more clearly state, does it seem to be listening for same.

I wonder if this is related to the problem I am having getting a button in a drawer to respond to <Return>.
The drawer is in some ways like a sheet, except that it does not run modally. Not news to most folks here.

In the drawer I am using, I have a similar configuration to the original question in NextKeyViewDoesntWork, with an NSForm for data entry to a table in the main window. The button is not getting the keystroke (its appearance reflects the fact that it is not a responder for keystrokes - not blue and pulsating in the interface) even though I set its key equivalent to <Return> when defining properties for the button in IB.

I tried setting the     nextKeyView outlet of the NSForm. Of course, that didn't work - the form wants to trap tab keystrokes in circular fashion from the
end back to the beginning. That is, once I select one of the text fields in the form, the drawer is happy to respond to keystrokes and tabs from field to field in the form without problems. It would be nice if the button would respond to <Return> and use its action to write the data to the table.

I'm guessing this is because the drawer's content view returns     NO for     - ( BOOL ) acceptsFirstResponder - the default, is it not?
(I'm only guessing here. I haven't actually tested this hypothesis. Yet.)

This suggests that by subclassing and overriding this method, or just sending     makeFirstResponder to the button,
I might be able to make some progress. But there isn't an obvious moment to send the message, since I want the table to accept
incomplete data from the form, when not all fields have data. I don't want the cells in the form to perform any actions in the controller,
just the button.

What other options do I have? Should I just live with having to click the button?

----

When you open the drawer, try sending     setDefaultButtonCell: to the drawer's parent window and pass your button's cell as the argument.

For example:

    
- (IBAction)openDrawer:(id)sender
{
    // ...open the drawer...

    myDrawer parentWindow] setDefaultButtonCell:[myButton cell;
}

