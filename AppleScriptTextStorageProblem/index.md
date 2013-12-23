---
layout: page
title: AppleScriptTextStorageProblem
---

I have a document-based app that I'm trying to add applescript support to.

I've copied in TextEdit's scriptSuite and scriptTerminology files, and exposed my textStorage via:

- (NSTextStorage*) textStorage ;
- (void)setTextStorage:(id)ts;

I can create an applescript like so:

tell application "asstuff" to set text of document 1 to "Hello World"

And it updates my text view with "Hello World".  However, when I try to pull back out the text like this:

tell application "asstuff" to set t to (text of document 1)

ScriptEditor throws a "asstuff got an error: NSReceiverEvaluationScriptError: 3" and highlights the word "text" in the script.

If I replace "asstuff" with "TextEdit", everything works fine, so I don't think it's the scriptSuite that is the problem.

I've looked through TextEdit's source code, and I don't see it doing anything special to support the second script.  Am I missing anything obvious?  I've pored over Apple's Cocoa & and AppleScript documentation, and it looks like I'm doing everything correctly.

