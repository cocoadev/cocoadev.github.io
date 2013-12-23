---
layout: page
title: TheMethodDefinitionAndTheSemicolon
---

A short story by General/NedO:

While reading assorted Cocoa sources today I came across and interesting semicolon usage that is not familiar to me. I certainly make no claim of anything resembling expertise in Cocoa, but I do have a solid C background. These strange semicolons confused me somewhat and after a couple hours of seaching assorted online resources I am still at a bit of a loss. The best explanation of the semicolon is the code itself.

    
// Random method chosen for illustrative purposes only.
// This version of the method has no semicolon and works fine.
- (id) initWithWindowNibName:(General/NSString *) windowNibName
{
    self = [super initWithWindowNibName:windowNibName];
    return self;
}

// Second version with a semicolon at the end of the line.
// This version also works fine
- (id) initWithWindowNibName:(General/NSString *) windowNibName;
{
    self = [super initWithWindowNibName:windowNibName];
    return self;
}


With many methods this seems to be of no consequence. As an aside it does change the method's name's appearance in the General/BBEdit function list pop-up from normal text sans semicolon to italicized text with semicolon. There are some methods/functions however that this does not work with, most obviously:

    
int main(int argc, const char *argv[]);
{
    return General/NSApplicationMain(argc, argv);
}


This will fail to compile, returning a syntax error. Is this because this is a function and not a method? That is my thinking, but that didn't answer what that semicolon is doing there.

Another example, taken from General/TheOmniGroup 's General/OmniFoundation -- General/OFNull class:
    
- (General/NSString *)descriptionWithLocale:(General/NSDictionary *)locale
                             indent:(unsigned)level
{
    return @"*null*";
}

- (General/NSString *)description;
{
    return @"*null*";
}


Two methods, one with semicolon and one without. I thought at first it might be the presence of multiple params but that still doesn't answer the question.

So I hereby declare myself King of the Morons and do stoop to asking what seems to me to be the most basic of syntactical questions: What is that semicolon, why is it there, what does it do and does any of this really matter?

-General/NedO

----

It's an Obj-C thing so you can just copy/paste the method signature line from your header file.

----

Like the previous guy said.  It's just a convenience.  Personally, I would think it rather bad style to leave the semicolon in, but it will work anyway if you do it by mistake. 

Interestingly, I checked the Objective-C grammar, and it does not include the optional semicolon.  I suppose this is a gcc feature then.

*It's been there since General/NeXTStep, apparently, since Andrew Stone claims to have been using it since then. I don't think General/NeXTStep used GCC, did it?*

According to [http://www.kernelthread.com/mac/oshistory/7.html]  *"Cox and Love founded General/StepStone Corporation, from which General/NeXT licensed the language and created their own  compiler. In 1995, General/NeXT acquired all rights to General/StepStone's Objective-C related intellectual property. Apple's Objective-C compiler used in Mac OS X is a modified version of the GNU compiler"*

----

There's an Obj-C grammar? where?

----

Google is your friend.

http://www.wodeveloper.com/omniLists/macosx-dev/2001/March/msg00807.html
Objective-C grammar for Lex/Yacc Flex/Bison as I have typed it in from
relevant books, compiled, and tested.  I had forgotten about this.

http://www.antlr.org/1.33/contrib/General/ObjC.g ANTLR-based grammar

http://developer.apple.com/documentation/Cocoa/Conceptual/General/ObjectiveC/General/LanguageGrammar/chapter_6_section_1.html
Close to BNF grammar

http://www.toodarkpark.org/computers/objc/grammar.html Close to BNF grammar

http://www.channelu.com/General/NeXT/General/NeXTStep/3.3/nd/Concepts/General/ObjectiveC/B_Grammar/Grammar.htmld/
Close to BNF grammar

http://64.233.167.104/search?q=cache:ioUWL8-tZMMJ:plg.uwaterloo.ca/~cforall/gcc.y.pdf+Objective-c+grammar&hl=en
YACC grammar for C and Objective-C extensions

Plus of course the GNU Compiler Collection which includes Objective-C and is open source and has a bison grammar :)

*cool :) I had been told there wasn't one. That'll teach me to believe anything I'm told...*

----

Very useful to have the typed in grammar!  But I see I should have been more specific.  :-)  The optional semicolon is not in the objective-C grammar given in the back of Apple's online General/ObjC book.

http://developer.apple.com/documentation/Cocoa/Conceptual/General/ObjectiveC/General/LanguageGrammar/chapter_6_section_1.html

----

*What did the method definition say to the semicolon?*

----

The fact that the semicolon being in this place is a) causing so much confusion and b) Andrew Stone feels he has to write an essay to justify its use because there is so much confusion, that using it just isn't a Good Thing.  Seems bizarre to go to all this effort just because you're lazy on the cut-n-paste.

----

*I don't know... what did the method definition say to the semicolon?*

'I *told* you that appendix was bad news!'

Heh - you know, I've been trying to think of a response to that for a few days. I guess that's as good as any. ;-)

----

Right now the best parser reference might be the newly-emerging "clang" LLVM C front end compiler, which 
can be found at http://llvm.org/svn/llvm-project/cfe/trunk/Parse/ . It's a recursive-descent parser (funny
how what's old is new again).

----

The semi-colon can be useful to search and replace it with an empty method body in order to expand a header into the implementation. WARNING: this only works when you initially create the implementation, not once you start populating it with code! So, if you have a fairly good definition of a class you want to write in advance (you do, don't you? ;-) then create its interface in its entirety, cut and paste the methods to the implementation, type in one method body as you want it then select and copy it, open the Find/Replace dialog and replace ';' with the pasted method body. This can save mucho typing when you first create your implementation, and it also neatly removes all the unneeded trailing semi-colons. --GC
