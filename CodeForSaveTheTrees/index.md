---
layout: page
title: CodeForSaveTheTrees
---

Code from the description/discussion of SaveTheTrees.

    
#import <Foundation/Foundation.h>

void putClassArtInDir(id path);

@interface NSArray (UmArrayExt) 
-(NSMutableArray *)pathsWithExtension:(NSString *)ext;
@end

@interface NSString (UmStringExt)
-(NSArray *)pullSinglesWithStartTag:(NSString *)startTag endTag:(NSString *)endTag range:(NSRange)range;
@end

@interface UmDocEdit : NSObject {}
+(void)editTableOfContentsAtPath:(NSString *)file;
+(void)editClassAtPath:(NSString *)file;
+(void)editClassesInDirectory:(NSString *)dir;
+(NSRange)rangeOfMethodsInHTML:(NSString *)file;
@end

@interface UmHTML : NSObject {}
+(NSString *)tableWithArray:(id)array columns:(int)columns upDown:(BOOL)upDown;
+(NSString *)tableWithAttributes:(NSDictionary *)att;
@end

@implementation NSArray (UmArrayExt) 
-(NSMutableArray *)pathsWithExtension:(NSString *)ext {
    id objEnum=[self objectEnumerator];
    NSMutableArray *returnArray=[NSMutableArray array];
    id path;
    while (path=[objEnum nextObject]) if (path pathExtension] isEqualToString:ext]) [returnArray addObject:path];
    return returnArray;
}
@end

@implementation [[NSString (UmStringExt)
-(NSArray *)pullElementsWithStartTag:(NSString *)startTag endTag:(NSString *)endTag range:(NSRange)range {
    NSMutableString *tempString=[NSMutableString stringWithString:[self substringWithRange:range]];
    NSMutableArray *elements=[NSMutableArray array];
    NSRange startTagRange, endTagRange, elementRange;
    while (1) {
        startTagRange=[tempString rangeOfString:startTag];
        if (startTagRange.length>0) [tempString deleteCharactersInRange:NSMakeRange(0, startTagRange.location)];
        else break;
        endTagRange=[tempString rangeOfString:endTag];
        if (endTagRange.length==0) break;
        elementRange=NSMakeRange(0, endTagRange.location+endTagRange.length);
        [elements addObject:[tempString substringWithRange:elementRange]];
        [tempString deleteCharactersInRange:elementRange];
    }
    return elements;
}
@end

@implementation UmDocEdit 
+(void)editTableOfContentsAtPath:(NSString *)file {
    NSString *toc=[NSString stringWithContentsOfFile:file];
    NSString *dir=[file stringByDeletingLastPathComponent];
    NSMutableString *quickTOC=[NSMutableString string];
    NSString *name=file lastPathComponent] stringByDeletingPathExtension];
    [[NSArray *classes, *protocols;
    NSString *classTable, *protocolTable;
    protocols=[toc pullElementsWithStartTag:@"<a href = \"Protocols/" endTag:@"</a>" range:NSMakeRange(0,[toc length])];
    classes=[toc pullElementsWithStartTag:@"<a href = \"Classes/" endTag:@"</a>" range:NSMakeRange(0,[toc length])];
    protocols=[protocols sortedArrayUsingSelector:@selector(caseInsensitiveCompare:)];
    classes=[classes sortedArrayUsingSelector:@selector(caseInsensitiveCompare:)];
    classTable=[UmHTML tableWithArray:classes columns:4 upDown:YES];
    protocolTable=[UmHTML tableWithArray:protocols columns:4 upDown:YES];
    [quickTOC appendFormat:@"<HTML>\n\t<HEAD>\n\t<TITLE>%@</TITLE>\n\t</HEAD>\n<BODY>\n", name];
    [quickTOC appendFormat:@"<h1>%@</h1><br><br>\n", name];
    [quickTOC appendFormat:@"<b>Classes</b><br><hr><br>\n%@\n", classTable];
    [quickTOC appendFormat:@"<br><br><b>Protocols</b><br><hr><br>\n%@\n", protocolTable];
    [quickTOC appendString:@"</BODY>\n</HTML>\n"];
    [quickTOC writeToFile:[dir stringByAppendingPathComponent:[NSString stringWithFormat:@"%@.html", name]] atomically:YES];
    
}
+(void)editClassesInDirectory:(NSString *)dir {
    NSFileManager *myManager=[NSFileManager defaultManager];
    NSArray *htmlFiles=myManager directoryContentsAtPath:dir] pathsWithExtension:@"html"];
    [[NSEnumerator *objEnum;
    id obj;
    
    htmlFiles=[dir stringsByAppendingPaths:htmlFiles];
    objEnum=[htmlFiles objectEnumerator];
    while (obj=[objEnum nextObject]) {
        NSLog(obj);
        [UmDocEdit editClassAtPath:obj];
    }
}
+(void)editClassAtPath:(NSString *)file {
    int i;
    NSMutableString *html=[NSMutableString stringWithContentsOfFile:file];
    NSRange rangeOfMethods=[UmDocEdit rangeOfMethodsInHTML:html];
    NSArray *lines, *elements;
    NSString *line;
    NSEnumerator *objEnum;
    if (rangeOfMethods.length==0) return;
    NSString *API=[file stringByDeletingLastPathComponent] stringByDeletingLastPathComponent] lastPathComponent];
    [[NSString *blockOfMethods=[html substringWithRange:rangeOfMethods];
    NSArray *methods=[blockOfMethods pullElementsWithStartTag:@"<a href=\"#" 
                                                            endTag:@">" 
                                                            range:NSMakeRange(0, [blockOfMethods length])];
    NSRange methodRange;
    NSMutableArray *internalLinks=[NSMutableArray array];
    NSRange top=[html rangeOfString:@"</h1>"];
    NSString *classTag=file lastPathComponent] stringByDeletingPathExtension];
    lines=[blockOfMethods componentsSeparatedByString:@"\n"];
    objEnum=[lines objectEnumerator];
    [[NSString *buttonElement=[NSString stringWithFormat:@"<br><a href=\"../%@TOC.html\"><img src=\"../Art/up.gif\" border=\"0\"\
alt=\"Table of Contents\"></a>   <a href=\"#//apple_ref/occ/cl/%@\"><img src=\"../Art/class.gif\" border=\"0\" alt=\"Table of \
Contents\"></a><br>", API, classTag];
    NSArray *buttonArray=[NSArray arrayWithObjects:buttonElement, @"", @"", buttonElement, nil];
    NSMutableString *buttonTable=[UmHTML tableWithArray:buttonArray columns:4 upDown:NO];
    [buttonTable appendString:@"<br>"];
    
    while (line=[objEnum nextObject]) {
        if ([line length]>0 && [line characterAtIndex:0]!='<') [internalLinks addObject:line];
        elements=[line pullElementsWithStartTag:@"<a href=\"#" endTag:@"</a>" range:NSMakeRange(0, [line length])];
        if ([elements count]>0) {
            line=[NSMutableString stringWithString:[elements objectAtIndex:0]];
            [internalLinks addObject:line];
        }
    }
    if ([internalLinks count]<40) {
        for (i=0;i<40-[internalLinks count];i++) {
            [internalLinks addObject:@""];
        }
    }
    id tableOfInternalMethodLinks=[UmHTML tableWithArray:internalLinks columns:4 upDown:YES];
    if (top.length>0 && tableOfInternalMethodLinks) {
        [html insertString:tableOfInternalMethodLinks atIndex:top.location+top.length];
        [html insertString:buttonTable atIndex: top.location+top.length + [tableOfInternalMethodLinks length]];
    }
    objEnum=[methods objectEnumerator];
    id method;
    while (method=[objEnum nextObject]) {
        method=[NSMutableString stringWithString:method];
        [method replaceCharactersInRange:[method rangeOfString:@"<a href=\"#"] withString:@"<a name=\""];
        methodRange=[html rangeOfString:method];
        if (methodRange.length>0) [html insertString:buttonTable atIndex:methodRange.location];
    }
    [html writeToFile:file atomically:YES];
    

}

+(NSRange)rangeOfMethodsInHTML:(NSString *)html {
    NSRange range, start, end1, end2, end;
    start=[html rangeOfString:@"<h2>Method Types</h2>"];
    end1=[html rangeOfString:@"<h2>Instance Methods</h2>"];
    end2=[html rangeOfString:@"<h2>Class Methods</h2>"];
        if (end1.length>0) end=end1;
    if (end2.length>0) end=end2;
    range=NSMakeRange(start.location, end.location-start.location);
    if (range.length>[html length]) range.length=0;
    if (start.length==0 || end.length==0) range.length=0;
    return range;
}
@end

@implementation UmHTML 
+(NSString *)tableWithArray:(id)array columns:(int)columns upDown:(BOOL)upDown {
    NSMutableDictionary *att=[NSMutableDictionary dictionaryWithObjectsAndKeys:
                    array, @"cells",
                    [NSString stringWithFormat:@"%i", columns], @"columns", nil];
    if (upDown) [att setObject:@"YES" forKey:@"upDown"];
    return [UmHTML tableWithAttributes:att];
}

+(NSString *)tableWithAttributes:(NSDictionary *)att {
    NSMutableString *table=[NSMutableString string];
    int rows, columns, row, column;
    id border=@"0";
    id cellpadding=@"0";
    id cellspacing=@"0";
    id obj, cells;
    int cellCount=0;
    int count, remainder, i, ii;
    BOOL upDown=NO;
    id matrix[100][100];
    id widths;
    int widthValue;
    id comp;
    BOOL widthIsPercent;
    id tableWidth=[NSString stringWithFormat:@"100%c", '\%'];
    id width;
    id blank=@"";
    for (i=0;i<100;i++) for (ii=0;ii<100;ii++) matrix[i][ii]=blank;
    if (obj=[att objectForKey:@"border"]) border=obj;
    if (obj=[att objectForKey:@"cellpadding"]) cellpadding=obj;
    if (obj=[att objectForKey:@"cellspacing"]) cellspacing=obj;
    if (obj=[att objectForKey:@"width"]) width=obj;
    if (obj=[att objectForKey:@"tableWidth"]) tableWidth=obj;
    rows=0; columns=0;
    if (obj=[att objectForKey:@"columns"]) columns=[obj intValue];
    if (obj=[att objectForKey:@"upDown"]) upDown=YES;
    widths=[att objectForKey:@"widths"];
    cells=[att objectForKey:@"cells"];
    if (cells) {
        cellCount=[cells count];
        if (cellCount==0) return nil;
    }
    else return nil;
    id percent, widthText;
    if (columns==0) columns=4;

    if (widths) {
        if ([widths count]!=columns) return @"column count does not match width info";
    }	
    else {
        comp=[tableWidth componentsSeparatedByString:@"\%"];
        if ([comp count]>0) {
            widthIsPercent=YES;
        }
        else widthIsPercent=NO;
        percent=[comp objectAtIndex:0];
        widthValue=[percent intValue];
        widthValue=widthValue/columns;       
        widths=[NSMutableArray array];
        if (widthIsPercent) widthText=[NSString stringWithFormat:@"%i%c", widthValue, '\%'];
        else widthText=[NSString stringWithFormat:@"%i", widthValue];
        for (i=0;i<columns;i++) {
            [widths addObject:widthText];
        }
    }
    rows=floor(cellCount/columns);
    remainder=cellCount-(rows*columns);
    if (remainder) {
        remainder=0;
        rows++;
    }
    count=0;
    if (upDown) {
        for (column=0;column<columns;column++) {
            for (row=0;row<rows;row++) {
                matrix[row][column]=[cells objectAtIndex:count];
                
                count++;
                if (count==cellCount) break;
            }
            if (count==cellCount) break;
        }
    }
    else {
        for (row=0;row<rows;row++) {
            for (column=0;column<columns;column++) {
                if (count<[cells count]) matrix[row][column]=[cells objectAtIndex:count];
                count++;
            }
        }    
    }
    [table appendFormat:@"<table border=\"%@\" cellpadding=\"%@\" cellspacing=\"%@\" width=\"%@\">\n", 
                            border, 
                            cellpadding, 
                            cellspacing, 
                            tableWidth];
    count=0;
    for (row=0;row<rows;row++) {
        [table appendString:@"<tr>\n"];
        for (column=0;column<columns;column++) {
            [table appendFormat:@"\t<td width=\"%@\">%@</td>\n", [widths objectAtIndex:column],  matrix[row][column]];
            count++;
        }
        [table appendString:@"</tr>\n"];
    }
    [table appendString:@"</table>\n"];
    return table;
    
}

@end

int main(int argc, const char *argv[]) {

    NSAutoreleasePool *myPool=[[NSAutoreleasePool alloc] init];
    NSFileManager *myManager=[NSFileManager defaultManager];        
    NSString *workDir=[myManager currentDirectoryPath];
    NSString *appKitDir, *foundationDir;
    
    appKitDir=[workDir stringByAppendingPathComponent:@"AppKit"];
    foundationDir=[workDir stringByAppendingPathComponent:@"Foundation"];
    putClassArtInDir([appKitDir stringByAppendingPathComponent:@"Art"]);
    putClassArtInDir([foundationDir stringByAppendingPathComponent:@"Art"]);
    [UmDocEdit editTableOfContentsAtPath:[appKitDir stringByAppendingPathComponent:@"AppKitTOC.html"]];
    [UmDocEdit editTableOfContentsAtPath:[foundationDir stringByAppendingPathComponent:@"FoundationTOC.html"]];
    [UmDocEdit editClassesInDirectory:[appKitDir stringByAppendingPathComponent:@"Classes"]];
    [UmDocEdit editClassesInDirectory:[appKitDir stringByAppendingPathComponent:@"Protocols"]];
    [UmDocEdit editClassesInDirectory:[foundationDir stringByAppendingPathComponent:@"Classes"]];
    [UmDocEdit editClassesInDirectory:[foundationDir stringByAppendingPathComponent:@"Protocols"]];
    [myPool release];
    return 0;
}


void putClassArtInDir(id path) {

        NSMutableString *classArt=[NSMutableString string];
        [classArt appendString:@"<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n"];
        [classArt appendString:@"<!DOCTYPE plist PUBLIC \"-//Apple Computer//DTD PLIST \
1.0//EN\" \"http://www.apple.com/DTDs/PropertyList-1.0.dtd\">\n"];
        [classArt appendString:@"<plist version=\"1.0\">\n<dict>\n<key>data</key>\n"];
        [classArt appendString:@"<data>\
        R0lGODlhSAARAOcAAP////7+/ubm5q6uroWFhYCAgISEhHd3d3FxcX5+foaGhn19fXx8\
        fH9/f3Jycnl5eYGBgZCQkG9vb4eHh4ODg3R0dHh4eHZ2dnV1dXNzc8zMzLu7u+Li4tzc\
        3OPj4+rq6t3d3e7u7t/f39nZ2ff399vb2+jo6O3t7eTk5ODg4Ovr6/Dw8OXl5ezs7PT0\
        9NPT0+fn5+np6dXV1fz8/O/v7+Hh4fX19dbW1vPz8/n5+fLy8vr6+v39/fHx8aioqMPD\
        w9fX1/v7+8rKys/Pz83NzVhYWGpqas7OzsDAwNHR0dLS0tDQ0NTU1NjY2N7e3sTExMnJ\
        yQAAAL+/v8fHx729vcHBwYmJibm5ube3t7q6usjIyGRkZF1dXWJiYsbGxggICA4ODgMD\
        AwUFBb6+vtra2llZWWdnZ2NjYwsLC/j4+GFhYXBwcFtbW2lpaQYGBgICAhYWFgEBARAQ\
        EGBgYG5ubmVlZQcHB/b29mhoaAoKCgQEBGZmZmtraxISEgwMDF9fXxUVFRcXF3p6ehER\
        ERkZGbS0tKamppGRkcXFxbCwsKurq6mpqcvLy7Ozs5WVlYqKioKCgqGhoZiYmP//////\
        ////////////////////////////////////////////////////////////////////\
        ////////////////////////////////////////////////////////////////////\
        ////////////////////////////////////////////////////////////////////\
        ////////////////////////////////////////////////////////////////////\
        ////////////////////////////////////////////////////////////////////\
        ////////////////////////////////////////////////////////////////////\
        /////////////////////yH+FUNyZWF0ZWQgd2l0aCBUaGUgR0lNUAAsAAAAAEgAEQAA\
        CP4AAQgcODCAgAEEChg4UABBAgUJFjBo4EABgwMGHhBIwOABBAUOIkgwMIFCBQMWOhYg\
        cAFDSgYMFCzgSCEDhAsJIECooEDRiTQEgwLQMGEDhw4ePoAI4UHECA8kSngwcQLFCRMp\
        VKhYwaKFCw0mTLyAgSLFCgExYMiYMYMFDQ81WAjwcIKFjRgtBICI4eLFFAxQhQo0MeEG\
        DBsAXACYgXggDoEtBK4AQCLHDAA6TgTdIVAHj4EkYvQAEELgZxwfKKcGQENgBxUVVAgG\
        4OMHkBxBOggZAoJIESMvjiBJ8kLJkhdMNNxo4uTJEBRQNESJsiGFFBFTiKSgMoIKjBsa\
        iP6MqKJhgxIOxD08YaJEikAiiBbNtnIFC4AsWjgokbFlC5cuQqxAhAzi3dCBFzBM9wUY\
        0y0Rhhgi3CADEx54MIYATmjAxBJKHMHBCx96UAUZULwgAyNkcNAIAFQU4shsDiTRgRJK\
        1FDCDWVIYIYEZ0jwRAxM0ODECEqsEAUaKIzAgQklkDCdB0v0wAQQNQhwRAcxoCCDCCzU\
        WAIZKaCXBhQn1BBDE0kAoUETfMxnwoQ4pMCCGmuwcYYaZ7QBARQppKCBEwBMJwIHIoAA\
        glNRuFHCCVG8MR0KNTCRQhRwRBHHCkCYIEcUX0QBhA5HeIEaEDQwwgEEszGQQgcBAIHC\
        Gv5nzEGHGXW0McccdQgggg01cBCFHCLEpYIAMLgQRRgxpBCHADTYEcUJKzgaQwglqOAB\
        px9YBUMJK/QwQgs1mLBCCgnMdsAdHoCgQxd4GLEFHnXUYQa8eEjQhAc9pBBGHiyEQMYK\
        ZKjAwhtvtOBECCc4kUcUIbCgRxR2tDDCHR2gceRcXHGAAws12NABCyswMFsDHaQQBAfJ\
        nrDHFnV00QUfMOCgWQ+ARqFHCzrEEMQHrdl8ggpicDodDjiAEMZ0faDgQgxR+DHdCC7w\
        gIIOJrTgQRoyPDLbBCD00AIKAfBwghl/tEEHH0UAwEFZAODQwnQ2IIzCDiqU8DAKFrsw\
        Q/4UgLTAAwsAMJroBybwIEIUgciRggAfBLHCCR+EIAIkswniAs1S4+AEHS5vsQcXALRA\
        Ag4ccCBbFGKQQMMMIuSxdxhGinGCB06T4KsOPcMeBQlp9HDsHSfY0IJXKowAAAKzEUCs\
        CzbQ4EIAatCxBh9mOACEC3e0cBkNNNgwHYNB5zBdz5YO4iwPQU8XBgvi2xwFIT1wAMAd\
        HIQQQAghmEC5YIUYcgdjO8gBAGCwA7aQYAckYA0NdOACGhRQIALA3g4mkwMc2GAHAcQZ\
        AHrQvcvcAQc8uExlTFAZFQQgCAIZTSQKMRsnHKIE8hNIAARiGYHYACidAUAOXJBAHOIA\
        KIcBECBmEhgEEkwmdJe5DABiIBDOAIAHQrwDC5IgCRTMJgCIsIIUgJCGDoQgTGZCgQCu\
        cqYOtCAFTGhBBzqAgiR8ywksYOMNvuWBJohgcSbSwQiaEAMQwLEJNRCBE8hQFgGkgAxJ\
        YAAjZgOAGQogERaJyAImSclKWvKSmMykJivJEQUkQgCNDAgAOw==\
        </data>\
</dict>\
</plist>"];
CFStringRef errorString;
NSData *xmlData=[NSData dataWithBytes:[classArt cString] length:[classArt length]];
id plist=(id)CFPropertyListCreateFromXMLData(kCFAllocatorDefault, 
                                (CFDataRef)xmlData,
                                0, 
                                &errorString);
id artData=[plist objectForKey:@"data"];
NSLog(@"artPath: %@", path);
[artData writeToFile:[path stringByAppendingPathComponent:@"class.gif"] atomically:YES];
    
    
}






