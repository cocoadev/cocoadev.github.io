---
layout: page
title: TextViewProblems
---

I'm writing a HTML editor, I have some code that inserts some HTML code in the textview, the code is this "<meta name="author" content="Josha Chapman-Dodson, AKA devboy">" but it says I have all these undeclared functions.

----

That is one of the most insightful and interesting questions I have ever seen on General/CocoaDev. :)

----

I just ran into this last night and I thought it was great, so I'll have to post it here now.

General/HowToAskQuestions

----

I really hate to dignify your question with an answer. Here it is: escape the double quotes. And if you don't know what that means, hide. Hide now.
----
Ok, let me try this again. My code looks like this
        General/NSString *image;
    image = @"<img src="/images/htmledit.gif">";
    [textView insertText:image];
And my errors are this
    

*error: invalid operands to binary /
*'htmledit' undeclared
*error: parse error before string constant


I really need help with this.

| General/JoshaChapmanDodson |

----

Hey Josha,

You really need to brush up on your basic C programming skills. String assignments and string formating are topics you need to understand before even thinking about working with HTML. I guess you don't know what an escape sequence is. This is the root of your problem. 

Read the first 10 pages of "The C Programming Language" by Kernighan & Ritchie (ISBN 0131103709). In fact buy this book and keep it near your computer at all times.

Here's a link to get you started:

http://www.juicystudio.com/tutorial/c/basic.asp
 ----
I sort of /n escape thing is, but how do I implement it in the code in question?
 ----

[textView insertText:@"<img src=\"/images/htmledit.gif\">"]; <-- you really didn't help him with this

I'm surprised you haven't run into this a dozen times by now if you're writing an HTML editor.  

----
Thank you so much for all of you help! It works! : )

----

He is only going to come back and ask again without learning anything if you keep feeding him the code. 
----

I was feeding him for a day so that when he's hungry tomorrow he'll come back for fishing lessons.
----
Don't you enjoy making fun of me! : P

--General/JoshaChapmanDodson

*One of them does; the other is more interested in helping.*

I think everyone does, we just don't want to help him write all his code.
----
I'm not *asking* you to write my code, I'm just asking things that I need help with.

--General/JoshaChapmanDodson

----

If you get sick of escaping your quotes, instead of @"<img src=\"/images/htmledit.gif\">"; you can just use single quotes: @"<img src='/images/htmledit.gif'>"; It's valid XML
----
Thanks!
