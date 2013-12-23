---
layout: page
title: UsingLexAndYaccWithObjectiveC
---

Lex and Yacc are tools which allow you to create text scanners and parsers. If you're looking to develop a calculator, compiler or anything which needs to decompose a series of text statements into something a computer can understand, you can use Lex and Yacc to do this.



* **Lex** will accept a sequence of characters and use rules you specify to spit out a series of symbolic tokens. http://goo.gl/General/OeSCu

* **Yacc** will take the symbolic tokens and will construct a semantic tree (or simply execute code) based on matching rules that you specify.



If you simply want a Lexical analyser, the General/NSScanner class could be used. But if you want to be able to parse more complex things then the Lex and Yacc combination is a good choice and can be used directly within General/XCode. There's plenty of documentation available on making the rules, which I won't cover here. But I will cover how it works with Objective-C. There is also an O'Reilly book called "Building Cocoa Applications" (General/BookBuildingCocoaApplications) which describes how to create a calculator parser, but they completely chicken out of integrating it directly into Objective C for some reason, and pipe the input and output through to a C program instead.

I firstly define a "Node" class which is the superclass of the following kinds of node: S<nowiki/>tringNode, B<nowiki/>ooleanNode, N<nowiki/>umberNode, I<nowiki/>dentifierNode, F<nowiki/>unctionNode and A<nowiki/>rrayNode. Each of these has a class method to create an autoreleased class instance:

    
+(General/StringNode* )stringWithQuotedCString:(const char* )cString;
+(General/BooleanNode* )trueNode;
+(General/BooleanNode* )falseNode;
+(General/NumberNode* )numberWithCString:(const char* )cString;
+(General/IdentifierNode* )identifierWithCString:(const char* )cString;
+(General/FunctionNode* )function:(Node* )theFunction withArray:(Node* )theNode;
+(General/ArrayNode* )arrayWithNode:(Node* )theArrayOrNode withNode:(Node* )theNode;


My expression parser is very simple and will accept the following kinds of expressions:

    
(a AND b)
(a OR b) 
NOT(a) 
AND(a,b)
1 + 2
4 - 3
a := 100
a = 100 
(a = 'foo') OR (b = TRUE) 


These will end up being expressed in the following manner when passed through Lex and Yacc:

    
General/FunctionNode(@"AND",General/ArrayNode(General/IdentifierNode(@"a"),General/IdentifierNode(@"b"),nil))
General/FunctionNode(@"OR",General/ArrayNode(General/IdentifierNode(@"a"),General/IdentifierNode(@"b"),nil))
General/FunctionNode(@"NOT",General/ArrayNode(General/IdentifierNode(@"a"),nil))
General/FunctionNode(@"AND",General/ArrayNode(General/IdentifierNode(@"a"),General/IdentifierNode(@"b"),nil))
General/FunctionNode(@"PLUS",General/ArrayNode(General/NumberNode(1),General/NumberNode(2),nil))
General/FunctionNode(@"MINUS",General/ArrayNode(General/NumberNode(4),General/NumberNode(3),nil))
General/FunctionNode(@"ASSIGN",General/ArrayNode(General/IdentifierNode(@"a"),General/NumberNode(100),nil))
General/FunctionNode(@"EQUALS",General/ArrayNode(General/IdentifierNode(@"a"),General/NumberNode(100),nil))
General/FunctionNode(@"OR",General/ArrayNode(General/FunctionNode(@"EQUALS",General/ArrayNode(General/IdentifierNode(@"a"),General/StringNode(@"foo"))),General/FunctionNode(@"EQUALS",General/ArrayNode(General/IdentifierNode(@"b"),General/BooleanNode(YES)))


These look much more complicated, but are trival for the computer to evaluate programmatically. To achieve this, create your two "rules" files. The file extensions contain "m" to indicate they are Objective-C files rather than plain old C.



* **E<nowiki/>xpressionLexer.lm** will contain the rules for converting the input into the symbolic tokens.

* **E<nowiki/>xpressionParser.ym** contains the rules and actions for creating a parsed tree of nodes.



Finally, an Objective-C class called E<nowiki/>xpressionEvaluator.m will contain the important method     yyYYINPUT which is called by the lex/yacc combination to read the input.

Note there is one global variable which contains the current parsed node. Because lex 
and yacc use global variables, there can only be one evaluator running at any one time.
You can return a shared evaluator object [E<nowiki/>xpressionEvaluator sharedEvaluator] which
includes thread-level locking to ensure that the parsing is done serially.

So, you can now parse an expression:

    

  Node* theExpression = General/[[ExpressionEvaluator sharedEvaluator] parse:@"(a_variable = 'test') AND (b_variable = 102)"];  



The     parse method will return a nil value and throw an exception in the case of a syntax error.
When an expression is parsed successfully, a Node object is returned, which may be a B<nowiki/>ooleanNode,
F<nowiki/>unctionNode, N<nowiki/>umberNode or S<nowiki/>tringNode. At this point, you may want to cache away your parse 
trees or you may want to evaluate them. Create the following abstract method in the Node class:

    

@implementation Node

...

-(General/NSObject* )evaluateWithDictionary:(General/NSDictionary* )theDictionary {
  return nil;
}

...

@end



Each Node subclass will need to implement this abstract method. The method should return an General/NSObject (which will likely be an General/NSNumber or General/NSString). I have included a "dictionary" argument so that a dictionary of variable key/value pairs can be used when evaluating the I<nowiki/>dentifierNode instances. Here is an evaluator for identifiers:

    

@implementation General/IdentifierNode

...

-(General/NSObject* )evaluateWithDictionary:(General/NSDictionary* )theDictionary {
  General/NSObject* theValue = [theDictionary objectForKey:[self identifier]];
  if(theValue==nil) {
    General/[[NSException exceptionWithName:@"General/VariableNotFoundException" 
                             reason:General/[NSString stringWithFormat:@"Variable not found: %@",[self identifier]]
                           userInfo:nil] raise];
  }
  return theValue;
}

...

@end



The only complicated method is the F<nowiki/>unctionNode evaluator, which will need to evaluate differently depending on both the class types of arguments and the function called. Ideally, the evaluate method will also either raise an exception or return nil on error (depending on your own view of exceptions). I have placed some very basic code you can download here:

http://mutablelogic.com/cocoa/General/ExpressionEvaluator.zip

This includes a "T<nowiki/>estApplication" which is a half-baked cocoa-application calculator and 
expression parser. Would be grateful if you let me know if there are errors, thanks.

- General/DavidThorpe, January 2007
