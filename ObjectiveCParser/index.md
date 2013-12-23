---
layout: page
title: ObjectiveCParser
---

This might be better off on some other site but I'm hoping it might be useful to some cocoa person out there enough that they might help.

I am trying to build a Yacc parser to parse Objective C code. The parser would simply parse Objective C code. 
(If you do not know what a yacc or bison parser is, just take a computer science degree. :-> )
There does not seem to be a parser out there, (if you know of one, please tell).

Applications might include extensions to object reflection, blocks, objective-c interpreters, enhancements to package system, obj-C IDEs etc.

GCC uses a complicated yacc parser, (in all 3864 lines), which is coupled to gcc (look in GCC sources for - gcc/objc/objc-parse.y). A lot of grammar rules seem spurious for my purposes. 
So I got a C formal grammar ( http://www.lysator.liu.se/c/ANSI-C-grammar-y.html ) and the objective C grammar and put them together with a lexer. And lo, there were plenty of shift/reduce | reduce/reduce errors.
Big problems seem to be telling apart protocols, classnames, typenames and ordinary identifiers. There are some problems with the Objective C grammar as listed in "The Objective C Programming Language" - it needs a bit of reworking to get optional stuff like protocol lists working. I am not sure if anyone actually used that grammar anywhere.

I admit I am not (yet) proficient at parsing, grammars and such. I hope there may be some kind soul out there who can help.

MikeAsh has very kindly hosted the files. 

Anyway, here is the lexer: http://www.mikeash.com/objcparser/objC.lex
Here is the parser: http://www.mikeash.com/objcparser/objCParser.yacc
(which does not work and needs help)

Here is gcc's grammar, minus the code and other nasties: http://www.mikeash.com/objcparser/gcc-objc-parse.y

----
While the grammar is not complete (it was designed for the simpler task of documenting source) it is still a good example of integrating ObjC with lex and yacc code. See http://www.clindberg.org/ and look at the AutoDoc source code.

-jason

----

The Objective-C Programming Language grammar lists type-specifier as:

    
type-specifier:
void
char
short
int
long
float
double
signed
unsigned
id [ protocol-reference-list ]
class-name [ protocol-reference-list ]
struct-or-union-specifier
enum-specifier
typedef-name


However I often just use protocol-reference-list as a type-specifier, eg:

- (<AProtocol>) someFunction:(<BProtocol>);

which seems to be fine with gcc. I would certainly want to parse stuff like this. 
But making this part of the grammar interferes with many things.
I don't think I can trust that grammar anymore. Which leads me to ask: "Does anyone have the real grammar for Objective-C?" -- MikeAmy

*I think that works because unspecified types in ObjC default to id, so it interprets those untyped protocol declarations that way.* -- CharlesSteinman

I've never seen that syntax for protocols before, I always stick 'id' in front of them. Anyway, I assume that adding a raw protocol-reference-list to type-specifier runs into conflicts because of the <>'s around the list. What if you made a specialized type-specifier, call it method-type-specifier or something, which included all of the standard type-specifier things, plus the protocols?

I can certainly understand your desire for a "real grammar" for ObjC, but I don't think you will get it. There is no ObjC standard; the language is defined by its implementation. I think the closest thing you'll get to a real grammar is the grammar from one of the compilers. The reason I'm so interested in this parser is because it may provide a "real grammar" for ObjC that isn't totally inseparable from a compiler. -- MikeAsh

From the reading I've done on the GCC mailing lists it seems that Apple and the GCC team don't have a grammar for ObjC either, and have run into problems with this. Googling for "#import future" turns up some rather interesting arguments. --DavidSmith 
----

I came across the rule that allows <SomeProtocol> with no id, and I emailed the guy who put that in, he points out that "this feature might disappear when class protocols are added to the language." and referred to a discussion at http://lists.gnu.org/archive/html/discuss-gnustep/2003-09/msg00230.html. He also pointed out, as I suspected, that you can't really make a context free objective-c grammar because of typedef. One way round this might be to just treat typedefs and classes in the same way (which the gcc parser does in some places), but then that runs into problems since you can't have protocol references following a typedef name. 

 -- MikeAmy

Given up for now. This is just too much of a problem. For anyone looking for a grammar, starting with the GCC grammar would be the best idea but still pretty hard. So I have given up with this for now. Don't watch this space. -- MikeAmy

There's an Objective-C 2.0 grammar for ANTLR here: http://www.antlr.org/grammar/list (did not test it) -- Daniel Furrer

I got as far as an Objective-C grammar and outlines for parsers in C and Objective-C.

http://code.google.com/p/parce/

Maybe the grammar would be useful since it's for YACC. (I'm sorry the rest of the project is pretty hopeless; it was mooted by Objective-C 2 and the improved code completion in Xcode 3.) -- BrentGulanowski

