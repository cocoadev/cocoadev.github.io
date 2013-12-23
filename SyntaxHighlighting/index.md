---
layout: page
title: SyntaxHighlighting
---



General/SyntaxHighlighting is something that a lot of good code editors do to highlight different bits of code at a glance. To illustrate, use of Xcode with syntax highlighting will turn words like "int" and "id" one colour, but items in quotes will go quite another one.

General/OmniWeb has an HTML editor with good syntax highlighting that helps you avoid a lot of common errors like forgetting to close tags or using tags that aren't allowed.

To research adding syntax highlighting to your own code, study General/ImplementSyntaxHighlighting for sample code snippets.

----

I've written a syntax coloring text editor document class, General/UKSyntaxColoredTextDocument. As always, it can be found at http://www.zathras.de/programming/sourcecode.htm . It implements most of the things mentioned here, including localized updates of syntax coloring and using styles to aid in doing it directly.

It also loads most of the language-specific things and its list of identifiers from a .plist file that you can easily replace for your own languages, and it loads the colors from the defaults database so you can let your users customize them. Oh, and of course it also has "Go to line/character" menu item support, which is also useful if you want to show the user the line where a compiler error occurred or whatever.

-- General/UliKusterer

----

The source for General/TeXShop may be of some use:
http://www.uoregon.edu/~koch/texshop/texshop.html

Syntax Coloring Demo for Cocoa:
http://ranchero.com/cocoa/syntaxcoloring/

**See Also:**  General/HighlighterKit, General/IDEKit, General/SourceCodeKit
