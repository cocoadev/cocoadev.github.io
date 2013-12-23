---
layout: page
title: PurpleHelpButton
---



How do I programmatically link to my app's help docs, and ask it to load a specific page? Or, on the side, could I load a web site with Safari? ... How do I do either of those things in code when the user clicks on the purple button?

----

It'd be better to open it in the help book. Add this IBAction somewhere and then hook it up:

    - (IBAction)displayHelp:sender
{
    [[NSHelpManager sharedHelpManager] openHelpAnchor:@"YOUR_HELP_ANCHOR_HERE" inBook:@"YOUR_HELP_BOOK_TITLE_HERE"];
}


-- AndyMatuschak

----

Would anyone mind posting an example of what a help anchor looks like in a help book? Is it just a standard HTML anchor or does it have special requirements?

*Yeah, it's just a standard HTML anchor (though you have to run it through the help indexer for it to be able to find them). Feel free to consult Pixen's docs (http://opensword.org/downloads/pixenwithhelp.zip -- kinda in beta) for reference. -- AndyMatuschak*

