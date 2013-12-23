---
layout: page
title: ObjectiveCBlocks
---

Blocks are declared and typed as follows:

'return type' (^)('parameter types')

An example of a block that returns an int for two strings would be:

    
int (^)(NSString*, NSString*)


An example of a block that returns nothing from no arguments would be:

    
void (^)(void)


Blocks can also be declared as local variables in Objective-C code like so:

    
int main(int argc, string[] *argv)
{
  int(^x)(int input) = ^(int input){ return input * input; };

  int sixteen = x(4);
}



*The original message announcing blocks to the Clang list: http://lists.cs.uiuc.edu/pipermail/cfe-dev/2008-August/002670.html
*Blocks on the Clang site: http://clang.llvm.org/docs/LanguageExtensions.html#blocks
*MikeAsh's introduction: http://www.mikeash.com/?page=pyblog/friday-qa-2008-12-26.html
*A backport of blocks for Leopard and iPhoneOS: http://www.plausiblelabs.com/blog/?p=8 http://code.google.com/p/plblocks/
*A guide to using blocks in C and Objective-C: http://thirdcog.eu/pwcblocks/



----
Ok but is it very different then callback function ?
----
Yes, blocks capture their parent function's scope, and can even access it after the parent function has returned. See http://en.wikipedia.org/wiki/Closure_(computer_science)

