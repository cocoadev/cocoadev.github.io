---
layout: page
title: DoubleClickInBoundTableView
---

What is the easiest way to display a sheet if a table row is double clicked (and the table is bound)? I think I can do it by hand, but I thought there might be an easier way using Bindings.

----

With respect, if you want help you will need to take the time to more carefully define your problem on this page. For example, what have you tried? In what way has what you tried failed? For more specific guidelines on creating pages of this nature, see HowToAskQuestions.

In answer to your request, I suggest you at least try to get double-clicking working without worrying about bindings. It works exactly the same and if you read the documentation for NSTableView the mechanism will be immediately apparent to you. Using bindings means you just have to ask your array controller for the current selection (if it matters) as opposed to your table's data source. Please read the documentation and restate your question if there's something specifically wrong with your attempts. General "how-do-I" questions (the answers to which are easily found by performing a simple search and reading the conceptual introduction documentation) are generally not very well received in any technical community.

----

(Is such massive egotism on the part of the previous poster necessary, or even welcome, in "any technical community?") --FC

(The I-know-the-answer-but-I-won't-tell-you response usually comes from someone who doesn't actually know the answer.) -CS

----
In a response that actually answers the question, as opposed to the first person who responded, I think you can find the setDoubleAction: method in the docs for NSTableView, which you can use to set an action to run when a user double clicks a row in your table view. With this in mind, you create said method wherever (probably in your tableview's delegate if it has one, if not a controller or other class), and then use this method to do what you want. I take it you know how to display sheets in Cocoa, if not then look at the Sheets topic in the ADC docs. You can access your table view's data in the method using an outlet from IB hooked up to your NSArrayController or NSTreeController, depending on how your data is set up. 

For example:

    
- (void)awakeFromNib {
     [myTableView setDoubleAction:@selector(myDoubleClickAction:)];
}

- (void)myDoubleClickAction:(id)sender {
     [NSApp beginSheet:mySheet modalForWindow:myWindow modalDelegate:self didEndSelector:@selector(mySheetDidEnd:returnCode:contextInfo:) contextInfo:nil];
}


Hope this helps! --LoganCollins
----
You can indeed use bindings to get a sheet to pop up in response to a double click in a table, but I don't think it's necessarily easier. NSTableView has bindings for the double-click target and double-click arguments, and you can use these to run any method (not just an IBAction with a single 'sender' argument). This is great for times when you want the target of the double-click to depend on the state of some data, such as the table's selection. It's also great if the method you want to call takes more than one argument. But in the usual case, where you just want to fire off an action in your controller, it's probably easier to just use the traditional doubleAction mechanism that LoganCollins describes above. On the other hand, it's very likely that you'll want to use bindings to fill in the data in your sheet based on the table's selection.

If you do want to get bindings working, all you need to do is to specify the target and the selector in the table's doubleClickTarget binding, and then specify the arguments in the table's doubleClickArgument bindings. (doubleClickArgument is a multiple-value binding, so when you bind doubleClickArgument, a new binding called doubleClickArgument2 shows up, and so on.)
-CS
---- 
Thanks for all of your suggestions. I can't find the doubleClickTarget or doubleClickArgument: for the NSTableView, there is only:

* enabled
* hidden
* font
* fontBold
* fontFamilyName
* fontItalic
* fontName
* fontSize
* content
* selectionIndexes
* sortDescriptors
* rowHeight

Can someone help me here?

----
Are you using MacOS X 10.4? The doubleClickTarget and doubleClickAction bindings only became available in Tiger. -CS

----
If you don't see the doubleClickTarget, you may not have selected the table view itself, but rather just have selected a column in the table view.
I have trouble selecting nested items in IB all the time. Sometimes I put the nib file window into column view mode and then select my way down 
a hierarchy. -DRS 

----
Note that in InterfaceBuilder 3 the selection of nested objects is extremely simple but very much non-obvious. The first click on a hierarchy selects the outermost object. Each subsequent click will select the next object down. To get at the table view, click to select the scroll view, then click again. This is not a double-click, but just two clicks on the table view area.

