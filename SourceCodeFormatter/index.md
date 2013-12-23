---
layout: page
title: SourceCodeFormatter
---

Does anyone have recommendations for a easy to use source code formatter? I'd like to batch reformat some source I've inherited that is nearly unreadable in its current state.

----

One thing that would be good to do is to specify what is meant by "nearly unreadable". And how many lines of code you've inherited. The only things that googled on "source code formatter for os x" were some things that purport to be related to PHP code. Would the search-and-replace facilities of a UNIX text editor help? Are you looking to meet some sort of local standard as far as formatting goes (including standards brought on by OCD, which is what sometimes happens in my case)? If the code has line breaks in the middle of     for statements, that would about be my definition of "nearly unreadable". The less restrictive google search is totally polluted by hits for source code formatters for the Windoze platform.

----

Essentially, I'd like to standardize the indentation and brace styles.

----
Is there something you don't like about Xcode's Format->Re Indent menu and the extensive preferences that go along with it ?

----
It doesn't go far enough. I want to pick one standard brace style, and have it fix the code so that it conforms to that style. GNU indent is obtuse, and I couldn't get astyle to do what I wanted.

----
Well, unless a volunteer comes forward with some information, your quickest bet is going to be to write a little C program that parses the contents of a source file, keeps track of nested-brace level and along with that matches it with indent level. Just slip a newline in there after every semicolon and watch out for     for statements. At least it wouldn't be "nearly unreadable" any more. If I ever get stranded on a desert island with nothing but my solar-powered laptop, I'll start writing it myself. Maybe the result will be immortalized here some day, assuming I can ever escape from that danged island. Hopefully, you won't regard it as "obtuse".

