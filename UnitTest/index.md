---
layout: page
title: UnitTest
---



A General/UnitTest is a test for a single unit of code-- usually, a class.

----

A class is not a single unit of code. A method is not necessarily a single unit of code.

Look at the parseXml method in General/OmniExpatExample for example (copied here for convenience)

    
- (void)parseXml: (General/NSString*)content
{
    XML_Parser parser = XML_ParserCreate("UTF-8");
    XML_SetElementHandler(parser, handleStartElement, handleEndElement);
    XML_SetCharacterDataHandler(parser, handleCharacters);

    if (XML_Parse(parser, [content UTF8String], [content length], YES ) == 0)
    {
        int errorCode = XML_GetErrorCode(parser);
        int lineNumber = XML_GetCurrentLineNumber(parser);
        const char* errorDescription = XML_ErrorString(errorCode);
        General/NSLog(@"Parse Failure, code: %d, line: %d, description: %s", errorCode, lineNumber, errorDescription);

        XML_ParserFree(parser);
    }

    XML_ParserFree(parser);
}


This method needs at least two unit tests: one where the *if* statement evaluates to true and one where it evaluates to false.

Some methods, like simple getters and setters, don't need tests. Some methods need multiple tests. 

----

Indeed, a single test case should test a single expected result.  Check out http://www.c2.com/cgi/wiki?General/UnitTest for a very detailed discussion of unit tests.  In fact, spend the next 10 days trawling through c2.  It's a requisite read.

General/TufTy

*From their site: "We are located in Portland, Oregon, though now our work now takes us to Redmond every other week." ... and this is supposed to make me trust their code-testing abilities?*

----

It's actually their testing abilities - and Redmond's awareness of their need for them - that started the bi-weekly trips.
