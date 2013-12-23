---
layout: page
title: ClassCasting
---

OK, here's the deal:

I have 2 NSDocument subclasses, one for dealing with HTML documents, one for dealing with text documents. Both implement the same methods, with HTML being treated slightly differently (saving with a .html extension, for one thing). I have some code that looks like:

    
NSDocument *outputDocument;

    if (YES == isHTML)
    {
	outputDocument = [[NSDocumentController sharedDocumentController] 
         openUntitledDocumentOfType: @"HTML Document" display:NO];
    }
    else
    {
	outputDocument = [[NSDocumentController sharedDocumentController] 
          openUntitledDocumentOfType: @"Text Document" display:NO];
    }

    [outputDocument doStuff];


Which gives warnings that 'NSDocument may not respond to doStuff'. Not a big deal, I guess, since my subclass does respond to doStuff, but I'd like to fix it if I can. I tried variations on     [([outputDocument class])outputDocument doStuff]; which didn't work. Is there a way to cast a class at runtime?

----

Duh. NeverMind... Just typing outputDocument as     id gets rid of the warnings... I should retake ObjC 101

----

I've never taken ObjC 101, but my natural (obsession with) tidiness leads me to ask, why not treat isHTML as a proper BOOL?

    
    if (isHTML) { ... }
    else { ... }


*It is a proper BOOL if it's being declared as such... it's just being explicitly tested for a YES value here - nothing wrong with it, just a matter of style. It's probably actually better to do it the first way, since if isHTML was mistakenly declared as a pointer: *

    
BOOL *isHTML


*it would generate a warning. Otherwise you're just testing for nil, which is probably not what you intended. Also note the use of YES as the left value, to avoid = vs. == errors.*

----

Another easy way to get rid of this warning is to declare -doStuff as an unimplemented category on NSDocument, similar to how an informal protocol is declared:
    
@interface NSDocument (DoStuffSupport)
- (void)doStuff;
@end

This way you'll suppress the warning but you won't lose all such warnings in the process. -- Bo

