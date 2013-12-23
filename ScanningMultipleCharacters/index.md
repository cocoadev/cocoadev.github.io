---
layout: page
title: ScanningMultipleCharacters
---

(This page derives from the ScanningStrings question, but because the question itself is very different, I felt it deserved it's own page.)

I'm working on a piece of code that goes into a TextView subclass and highlights quotes (and handles escapes quotes and checks for being in a comment first, etc.).  It works, almost perfectly - except for a rather minor snag.  I can scan up to a '#' (A PHP one-line comment character) and it works fine.  But I cannot for the life of me figure out how to be able to scan up to multiple characters (i.e. a "//").  So basically, how would I go about making the scanner recognize a //, /*, and */ even though they are not single characters.  My code is below.

Thanks in Advance!

    
    NSCharacterSet *charSet = [NSCharacterSet characterSetWithCharactersInString:@"\"\'#\n\r"];
    NSScanner * theScanner = [NSScanner scannerWithString:[self string]];
    [theScanner setCharactersToBeSkipped:[NSCharacterSet whitespaceCharacterSet]];

    BOOL inQuote = NO;
    BOOL inSQuote = NO;
    BOOL inComment = NO;
    BOOL inBlockComment = NO;

    unsigned int quotePosition;
    while ([theScanner isAtEnd] == NO)
    {
        if ([theScanner scanString:@"\"" intoString:NULL])
        {
            if (self substringWithRange:[[NSMakeRange([theScanner scanLocation]-2,1)]isEqualToString:@"\\"] == NO)
            {
                if (inQuote)
                {
                    [self colorRange:NSMakeRange(quotePosition-1,[theScanner scanLocation]-quotePosition+1)withColor:[NSColor redColor]];
                    inQuote = NO;
                } else if (inQuote == NO && inSQuote == NO && inComment == NO && inBlockComment == NO)
                {
                    inQuote = YES;
                    quotePosition = [theScanner scanLocation];
                }
            }
        }

        if ([theScanner scanString:@"\'" intoString:NULL])
        {
            if (self substringWithRange:[[NSMakeRange([theScanner scanLocation]-2,1)]isEqualToString:@"\\"] == NO)
            {
                if (inSQuote)
                {
                    [self colorRange:NSMakeRange(quotePosition-1,[theScanner scanLocation]-quotePosition+1)withColor:[NSColor redColor]];
                    inSQuote = NO;
                } else if (inQuote == NO && inSQuote == NO && inComment == NO && inBlockComment == NO)
                {
                    inSQuote = YES;
                    quotePosition = [theScanner scanLocation];
                }
            }
        }

        if ([theScanner scanString:@"#" intoString:NULL] || [theScanner scanString:@"//" intoString:NULL])
        {
            if (inQuote == NO && inSQuote == NO)
            {
                inComment = YES;
            }
        }


        if ([theScanner scanString:@"\n" intoString:NULL] || [theScanner scanString:@"\r" intoString:NULL])
        {
            if (inComment)
            {
                inComment = NO;
            }
        }

        if ([theScanner scanString:@"/*" intoString:NULL])
        {
            inBlockComment = YES;
        }

        if ([theScanner scanString:@"*/" intoString:NULL])
        {
            inBlockComment = NO;
        }

        [theScanner scanUpToCharactersFromSet:charSet intoString:nil];
    }

