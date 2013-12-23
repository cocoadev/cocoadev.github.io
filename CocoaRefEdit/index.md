---
layout: page
title: CocoaRefEdit
---

Here�s a tool that edits the new references to Cocoa that comes with the development tools distributed with Panther. This tool places useful buttons and tables to help you navigate the docs more efficiently. It�s in the spirit of SaveTheTrees, except you can now choose the number of columns you would like to view. To use the tool do the following:


*Launch Xcode
*Create a new project (File -> New Project -> Foundation Tool)
*Name you project something like �cocoarefedit�
*Open main.m in an editor (cocoarefedit/source/main.m)
*Delete all of the default template in main.m, then cut and paste the source below into the file.
*Save and build the tool, but don�t run it (unless you want a copy of a bunch of documentation sitting in your build directory).
*Launch Terminal app
*Change the directory to a location suitable for output and run �cocoarefedit� 


Do something like this in Terminal app:

    
cd ~
mkdir temp 
cd temp
mkdir Documentation
cd Documentation
mkdir Cocoa
cd Cocoa
cocoarefedit �c 4 


The option �-c� is used to designate the number of columns to format each quick view table. It takes about 7 seconds to modify Cocoa�s documentation on a 450Mhz G4, so playing with a couple of column counts is not that big of a deal. Placing the three index pages in Safari�s Bookmark Bar is a great way to keep all of Cocoa�s references a couple of clicks away. If �cocoarefedit� builds and runs properly, you should see a newly created folder named �Reference� in the directory you are working in. The three index pages are at the subpaths below. 


*Reference/ApplicationKit/ObjC_classic/ApplicationKit.html
*Reference/Foundation/ObjC_classic/Foundation.html
*Reference/WebKit/ObjC_classic/ WebKit.html



--zootbobbalu

    

#import <Cocoa/Cocoa.h>
#include "fcntl.h"
#include <unistd.h>

static NSFileManager *MANAGER;
static NSString *RESOURCE_DIRECTORY, *OUTPUT_DIRECTORY;
static NSMutableString *XML_STRING, *INPUT_BUFFER;
static NSMutableData *RANGE_BUFFER, *OUTPUT_BUFFER;
static NSString *API;
static NSMutableDictionary *PLIST;
static id ITEMS_TO_SKIP;
static int COLUMN_NUMBER;

void SetupBuffers();
static BOOL CopyAndEditReferenceFiles();
static BOOL EditIndex(id path);
static NSMutableString *TableWithTableData(id elements, int columnCount);
static NSArray *PullElementsWithTag(id html, id startTag, id endTag);
static NSRange RangeToEOLFromIndex(id line, unsigned int index);
static NSArray *DataElementsFromTable(id table, unsigned int columnCount);
static NSDictionary *PullOptions(int argc, char *argv[]);
static NSRange RangeOfMethodsInHTML(id file);
static BOOL EditClassesInDirectory(id dir);
static void PutClassArtInDir(id path);
static BOOL EditClassAtPath(id file);
static void CreatePlist();
static void AP(id string);
static BOOL LoadHTMLAtPathIntoInputBuffer(int htmlFD);
static BOOL WriteFromString(int fd, id string, NSRange range);

NSArray *PullElementsWithTag(NSString *html, id startTag, id endTag) {
    NSRange range, start, end;
    range = NSMakeRange(0, [html length]);
    id array = [NSMutableArray array];
    while (1) {
        start = [html rangeOfString:startTag options:NSCaseInsensitiveSearch range:range];
        end = [html rangeOfString:endTag options:NSCaseInsensitiveSearch range:range];
        if (start.length && end.length) {
            unsigned int length = end.location - start.location + end.length;
            id element = [html substringWithRange:NSMakeRange(start.location, length)];
            [array addObject:[NSMutableString stringWithString:element]];
            range = RangeToEOLFromIndex(html, end.location + 1);
        }
        else break;
    }
    return array;
}

BOOL LoadHTMLAtPathIntoInputBuffer(int htmlFD) {
    if (htmlFD == -1) return NO;
    int bytesRead;
    char *tempInputBuffer = (char *)[OUTPUT_BUFFER bytes];
    [INPUT_BUFFER setString:@""];
    while (bytesRead = read(htmlFD, tempInputBuffer, 4096)) {
        tempInputBuffer[bytesRead] = 0;
        [INPUT_BUFFER appendFormat:@"%s", tempInputBuffer];
    }
    return YES;
}

id ButtonTableWithAPINameAndClassName(id API, id classTag) {
    NSMutableString *buttonElement = [NSMutableString string];
    [buttonElement appendFormat:@"<td><a href=\"../%@.html\">", API];
    [buttonElement appendString:@"<img src=\"Art/up.gif\" border=\"0\" alt=\"Table of Contents"];
    [buttonElement appendString:@"\"></a><FONT COLOR=\"#FFFFFF\">A</FONT><a href=\"#//apple_ref"];
    [buttonElement appendFormat:@"/occ/cl/%@\"><img src=\"Art/class.gif\" border=\"0\"", classTag];
    [buttonElement appendString:@" alt=\"Table of  Contents\"></a></td>"];
    NSArray *buttonArray = [NSArray arrayWithObjects:buttonElement, @"", @"", buttonElement, nil];
    NSMutableString *buttonTable = TableWithTableData(buttonArray, 4);
    [buttonTable insertString:@"<br>" atIndex:0];
    [buttonTable appendString:@"<br>"];
    return buttonTable;
}

BOOL EditClassAtPath(id file) { 
    int i; 
    int fd = open([file cString], O_RDWR, 0);
    id lastPathComponent = [file lastPathComponent];
    if ([ITEMS_TO_SKIP containsObject:lastPathComponent]) return YES;
    if (!LoadHTMLAtPathIntoInputBuffer(fd)) return NO;
    NSMutableString *html = INPUT_BUFFER;
    NSRange rangeOfMethods = RangeOfMethodsInHTML(html);
    int htmlLength = [html length];
    lseek(fd, 0, SEEK_SET);
                
    NSString *blockOfMethods = [html substringWithRange:rangeOfMethods];    
    
    NSMutableArray *internalLinks = [NSMutableArray array];
    NSString *classTag = [lastPathComponent stringByDeletingPathExtension];
    NSArray *lines = [blockOfMethods componentsSeparatedByString:@"\n"];
    NSEnumerator *lineEnum = [lines objectEnumerator]; NSString *line;
    while (line = [lineEnum nextObject]) {
        if ([line length] > 0 && [line characterAtIndex:0] != '<') {
            [internalLinks addObject:[NSMutableString stringWithFormat:@"<td>%@</td>", line]];
        }
        id elements = PullElementsWithTag(line, @"<td><a logicalPath", @"</a></td>");
        if ([elements count] > 0) {
            line = [NSMutableString stringWithString:[elements objectAtIndex:0]];
            [internalLinks addObject:line];
        }
    }
        
    NSString *tableOfInternalMethodLinks = TableWithTableData(internalLinks, COLUMN_NUMBER);
    NSString *buttonTable = ButtonTableWithAPINameAndClassName(API, classTag);

    NSRange top = [html rangeOfString:@"</h1>"];
    if (top.length > 0 && tableOfInternalMethodLinks) {
        if (!WriteFromString(fd, html, NSMakeRange(0, top.location + top.length))) return NO;
        if (!WriteFromString(fd, tableOfInternalMethodLinks, 
                        NSMakeRange(0, [tableOfInternalMethodLinks length]))) return NO;
        if (!WriteFromString(fd, buttonTable, NSMakeRange(0, [buttonTable length]))) return NO;
        NSRange midRange = NSMakeRange(top.location + top.length, 
                    (rangeOfMethods.location + rangeOfMethods.length) - (top.location + top.length));
        if (!WriteFromString(fd, html, midRange)) return NO;
    }

    NSRange subRange = RangeToEOLFromIndex(html, rangeOfMethods.location + rangeOfMethods.length);
    NSRange *ranges = (NSRange *)[RANGE_BUFFER bytes];
    int rangeCount = 1;
    ranges[0].location = subRange.location;
    NSRange rangeOfFirstHTag = [html rangeOfString:@"<h3>" options:nil range:subRange];
    NSRange searchRange;
    ranges[0].length = rangeOfFirstHTag.location - subRange.location;
    htmlLength = [html length];
    if (ranges[0].length) {
        searchRange = NSMakeRange(ranges[rangeCount - 1].location + 1, 
                                        htmlLength - ranges[rangeCount - 1].location - 1);
        while ((ranges[rangeCount] = [html rangeOfString:@"<h3>" options:nil range:searchRange]).length) 
        {
            ranges[rangeCount - 1].length = ranges[rangeCount].location - ranges[rangeCount - 1].location;
            rangeCount++;
            searchRange = NSMakeRange(ranges[rangeCount - 1].location + 1, 
                            htmlLength - ranges[rangeCount - 1].location - 1);
        }
        ranges[rangeCount - 1].length = subRange.location + subRange.length - ranges[rangeCount - 1].location;
        rangeCount++;
    }
    else rangeCount = 0;
    
    for (i = 1; i < rangeCount; i++) {
        if (!WriteFromString(fd, html, ranges[i])) return NO;
        if (!WriteFromString(fd, buttonTable, NSMakeRange(0, [buttonTable length]))) return NO;
    }
    close(fd);
    printf("%s ", file lastPathComponent] cString]);
    return YES;

}

BOOL [[WriteFromString(int fd, id string, NSRange range) {
    char *buffer = (char *)[OUTPUT_BUFFER bytes];
    int blocks = range.length / 4096;
    int remainder = range.length % 4096; 
    if (remainder) blocks++;
    int i; int loc = range.location;
    for (i = 0; i < blocks; i++) {
        range = (!(remainder && i == blocks - 1)) ? NSMakeRange(loc, 4096) : NSMakeRange(loc, remainder);
        [string getCString:buffer maxLength:[OUTPUT_BUFFER length] range:range remainingRange:nil];
        if (write(fd, buffer, range.length) != range.length) {
            NSLog(@"error writing to file!!");
            return NO;
        }
        loc += 4096;
    }
    return YES;
}


BOOL EditClassesInDirectory(id dir) {
    NSArray *htmlFiles = [MANAGER directoryContentsAtPath:dir];
    PutClassArtInDir([dir stringByAppendingPathComponent:@"Art"]);
    htmlFiles = [dir stringsByAppendingPaths:htmlFiles];
    id fileEnum = [htmlFiles objectEnumerator]; id file;
    while (file = [fileEnum nextObject]) { 
        if (file pathExtension] isEqualToString:@"html"]) if (![[EditClassAtPath(file)) return NO;
    }
    return YES;
}

NSRange RangeOfMethodsInHTML(NSString *html) {
    NSRange range, start, end1, end2, end;
    start = [html rangeOfString:@"<h2>Method"];
    end1 = [html rangeOfString:@"<h2>Instance" options:nil range:RangeToEOLFromIndex(html, start.location)];
    end2 = [html rangeOfString:@"<h2>Class" options:nil range:RangeToEOLFromIndex(html, start.location)];
    if (end1.length > 0 && end1.location > start.location) end = end1;
    if (end2.length > 0 && end2.location > start.location && end2.location < end.location) end = end2;
    range = NSMakeRange(start.location, end.location - start.location);
    if (range.length > [html length]) range.length = 0;
    if (start.length == 0 || end.length == 0) range.length = 0;
    return range;
}

NSArray *DataElementsFromTable(id table, unsigned int columnCount) {
    id rows = PullElementsWithTag(table, @"<tr", @"</tr"); 
    int i;
    id columns = [NSMutableArray array];
    for (i = 0; i < columnCount; i++) [columns addObject:[NSMutableArray array]];
    id rowEnum = [rows objectEnumerator]; id row;
    while (row = [rowEnum nextObject]) {
        id dataElements = PullElementsWithTag(row, @"<td", @"</td>");
        for (i = 0; i < columnCount; i++) {
            id column = [columns objectAtIndex:i];
            if (i < [dataElements count]) [column addObject:[dataElements objectAtIndex:i]];
            else columns objectAtIndex:i] addObject:@"<td></td>"];
        }
    }
    for (i = 1; i < columnCount; i++) [[columns objectAtIndex:0] addObjectsFromArray:[columns objectAtIndex:i;
    return (columnCount) ? [columns objectAtIndex:0] : columns;
}


NSMutableString *TableWithTableData(id elements, int columnCount) {
    int i, r, c; NSRange range;
    id columns = [NSMutableArray array];
    for (i = 0; i < columnCount; i++) [columns addObject:[NSMutableArray array]];
    int elementCount = [elements count]; 
    int outputRowCount = elementCount / columnCount;
    id tdTag = [NSString stringWithFormat:@"<td width=\"%i%c\">", (int)(100.0f / (float)columnCount), '%'];
    int padCount = elementCount % columnCount;
    for (i = 0; i < padCount; i++) [elements addObject:[NSMutableString stringWithFormat:@"%@</td>", tdTag]];
    int index = 0;
    for (i = 0; i < columnCount; i++) {
        id column = [columns objectAtIndex:i];
        for (r = 0; r < outputRowCount; r++) [column addObject:[elements objectAtIndex:index++]];
    }
    for (i = 0; i < elementCount; i++) {
        id element = [elements objectAtIndex:i];
        range = [element rangeOfString:@">"];  
        if (range.length) [element replaceCharactersInRange:NSMakeRange(0, range.location + 1)
                                                withString:tdTag];
    }
    id table = [NSMutableString stringWithString:@"<table border=\"0\" "];
    [table appendString:@"cellpadding=\"0\" cellspacing=\"0\" width=\"100%\">\n"];
    for (r = 0; r < outputRowCount; r++) {
        [table appendString:@"<tr>"];
        for (c = 0; c < columnCount; c++) [table appendString:columns objectAtIndex:c] objectAtIndex:r;
        [table appendString:@"</tr>\n"];
    }
    [table appendString:@"</table>\n"];
    return table;
}

NSRange RangeToEOLFromIndex(NSString *line, unsigned int index) {
    unsigned int length = [line length];
    if (index > length) return NSMakeRange(0, 0);
    else return NSMakeRange(index, length - index);
}


BOOL EditIndex(id path) {
    id indexSource = [NSString stringWithContentsOfFile:path];
    id APIDirectory = path stringByDeletingLastPathComponent] stringByDeletingLastPathComponent];
    id tables = [[PullElementsWithTag(indexSource, @"<table", @"</table");
    id name = [APIDirectory lastPathComponent];
    id newIndex = [NSMutableString stringWithFormat:@"<HTML>\n<TITLE>%@", name];
    [newIndex appendFormat:@" API</TITLE>\n<BODY>\n<br><H1>%@ API</H1><br><b>Classes</b>\n", name];
    int i;
    for (i = 1; i < 3; i++) {
        id table = [tables objectAtIndex:i];
        id dataElements = DataElementsFromTable(table, 2);
        id fourColumnTable = TableWithTableData(dataElements, COLUMN_NUMBER);
        [newIndex appendFormat:@"<hr>%@<br><br>", fourColumnTable];
        if (i == 1) [newIndex appendString:@"\n<b>Protocols</b>\n"];
    }
    [newIndex appendString:@"<BODY><HTML>"];
    id writePath = [NSString stringWithFormat:@"%@/ObjC_classic/%@.html", APIDirectory, name];
    return [newIndex writeToFile:writePath atomically:YES];
}

BOOL CopyAndEditReferenceFiles() {
    id foundationItemsToSkip = [NSArray arrayWithObjects:@"NSCountCommand.html", @"NSDeserializer.html", 
            @"NSExistsCommand.html", @"NSGetCommand.html", @"NSMessagePort.html", 
            @"NSMiddleSpecifier.html", @"NSNull.html", @"NSPropertyListSerialztion.html", 
            @"NSRandomSpecifier.html", @"NSSerializer.html", @"NSPropertySpecifier.html", nil];
    id appKitItemsToSkip = [NSArray arrayWithObjects:@"NSSecureTextField.html", @"NSPlaceholders.html", 
            @"NSTextAttachmentCell.html", nil];
    id webKitItemsToSkip = [NSArray arrayWithObjects:@"WebDownload.html", nil];
    id APIPathInfo = [NSMutableDictionary dictionaryWithObjectsAndKeys:        
                        appKitItemsToSkip, @"ApplicationKit/ObjC_classic", 
                        foundationItemsToSkip, @"Foundation/ObjC_classic", 
                        webKitItemsToSkip, @"WebKit/ObjC_classic", nil];
    id files = [NSArray arrayWithObjects:@"Classes", @"Functions", @"index.html", @"Intro", 
                @"Protocols", @"TypesAndConstants", nil];
    id APIPathEnum = [[APIPathInfo allKeys] objectEnumerator]; id APIPath;
    [MANAGER changeCurrentDirectoryPath:OUTPUT_DIRECTORY];
    while (APIPath = [APIPathEnum nextObject]) {
        id parentDirectory = [APIPath stringByDeletingLastPathComponent];
        if (![MANAGER createDirectoryAtPath:parentDirectory attributes:nil]) return NO;
        if (![MANAGER createDirectoryAtPath:APIPath attributes:nil]) return NO;
        id fileEnum = [files objectEnumerator]; id file;
        ITEMS_TO_SKIP = [APIPathInfo objectForKey:APIPath];
        API = [APIPath stringByDeletingLastPathComponent];
        while (file = [fileEnum nextObject]) {
            id innerPool = [[NSAutoreleasePool alloc] init];
            if ([APIPath isEqualToString:@"WebKit/ObjC_classic"] && 
                    [file isEqualToString:@"Functions"]) continue;
            id sourcePath = [NSString stringWithFormat:@"%@/%@/%@", RESOURCE_DIRECTORY, APIPath, file];
            id destPath = [NSString stringWithFormat:@"%@/%@/%@", OUTPUT_DIRECTORY, APIPath, file];
            NSLog(@"ditto %@ %@", sourcePath, destPath);
            NSTask *task = [[[NSTask alloc] init] autorelease];
            [task setArguments:[NSArray arrayWithObjects:sourcePath, destPath, nil]];
            [task setLaunchPath:@"/usr/bin/ditto"]; [task launch]; [task waitUntilExit];
            BOOL (*editFunction)(id path);
            if ([file isEqualToString:@"index.html"]) editFunction = EditIndex;
            else if ([file isEqualToString:@"Classes"] || [file isEqualToString:@"Protocols"]) 
                                editFunction = EditClassesInDirectory;
            else editFunction = nil;
            if (editFunction && !editFunction(destPath)) {
                NSLog(@"Unable to edit resource at path: %@", destPath); return NO;
            }
            [innerPool release];
        }
    }
    return YES;
}

NSDictionary *PullOptions(int argc, char *argv[]) {
    int i; id options = [NSMutableDictionary dictionary];
    for (i = 0; i < argc; i++) {
        if (strstr(argv[i], "-") == argv[i]) {
            id key = [NSString stringWithFormat:@"%s", &(argv[i++])[1]];
            id value = (i < argc) ? [NSString stringWithFormat:@"%s", argv[i++]] : @"";
            [options setObject:value forKey:key];
        }
    }
    return options;
}


void AP(id string) {[XML_STRING appendString:string];}

void CreatePlist() {
    XML_STRING = [NSMutableString string];
    AP(@"<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n");
    AP(@"<!DOCTYPE plist PUBLIC \"-//Apple Computer//DTD PLIST ");
    AP(@"1.0//EN\" \"http://www.apple.com/DTDs/PropertyList-1.0.dtd\">\n");
    AP(@"<plist version=\"1.0\">\n<dict><key>classIconData</key><data>\n");
    AP(@"R0lGODlhJQARAPcAAAAAAAEBAQICAgMDAwQEBAUFBQYGBgcHBwgICAkJCQoKCgsLCwwM");
    AP(@"DA0NDQ4ODg8PDxAQEBERERISEhMTExQUFBUVFRYWFhcXFxgYGBkZGRoaGhsbGxwcHB0d");
    AP(@"HR4eHh8fHyAgICEhISIiIiMjIyQkJCUlJSYmJicnJygoKCkpKSoqKisrKywsLC0tLS4u");
    AP(@"Li8vLzAwMDExMTIyMjMzMzQ0NDU1NTY2Njc3Nzg4ODk5OTo6Ojs7Ozw8PD09PT4+Pj8/");
    AP(@"P0BAQEFBQUJCQkNDQ0REREVFRUZGRkdHR0hISElJSUpKSktLS0xMTE1NTU5OTk9PT1BQ");
    AP(@"UFFRUVJSUlNTU1RUVFVVVVZWVldXV1hYWFlZWVpaWltbW1xcXF1dXV5eXl9fX2BgYGFh");
    AP(@"YWJiYmNjY2RkZGVlZWZmZmdnZ2hoaGlpaWpqamtra2xsbG1tbW5ubm9vb3BwcHFxcXJy");
    AP(@"cnNzc3R0dHV1dXZ2dnd3d3h4eHl5eXp6ent7e3x8fH19fX5+fn9/f4CAgIGBgYKCgoOD");
    AP(@"g4SEhIWFhYaGhoeHh4iIiImJiYqKiouLi4yMjI2NjY6Ojo+Pj5CQkJGRkZKSkpOTk5SU");
    AP(@"lJWVlZaWlpeXl5iYmJmZmZqampubm5ycnJ2dnZ6enp+fn6CgoKGhoaKioqOjo6SkpKWl");
    AP(@"paampqenp6ioqKmpqaqqqqurq6ysrK2tra6urq+vr7CwsLGxsbKysrOzs7S0tLW1tba2");
    AP(@"tre3t7i4uLm5ubq6uru7u7y8vL29vb6+vr+/v8DAwMHBwcLCwsPDw8TExMXFxcbGxsfH");
    AP(@"x8jIyMnJycrKysvLy8zMzM3Nzc7Ozs/Pz9DQ0NHR0dLS0tPT09TU1NXV1dbW1tfX19jY");
    AP(@"2NnZ2dra2tvb29zc3N3d3d7e3t/f3+Dg4OHh4eLi4uPj4+Tk5OXl5ebm5ufn5+jo6Onp");
    AP(@"6erq6uvr6+zs7O3t7e7u7u/v7/Dw8PHx8fLy8vPz8/T09PX19fb29vf39/j4+Pn5+fr6");
    AP(@"+vv7+/z8/P39/f7+/v///ywAAAAAJQARAAAI/wD/9Ss3KA+hPnvwAIrz6M0gQ4Lm3En4");
    AP(@"h1AdPHv47CnEp0+gOYBSscP3798yQ+HOfVunDh65ecvQnZNmbhw8c+jMTeNHzp04cOTM");
    AP(@"iSNXL50xOuPunTNUjd6/e/n4/ZNXsmq+qf2q3ov3r13JrOqeblNHZ52pYNb2KaN2rduw");
    AP(@"ZsiWAQCQC1wvY8u+8cLGq5oyZth+LcslDlq0f8yIoUKEq1YuYubmQqgA4NkBBt6qURMn");
    AP(@"rpe5bsucRWsWTlq4aL+yHfu3axYlOdC2RXsHYMI4bOK02Zsrzhk8a+DKNduGjpo3ctHC");
    AP(@"acsWDho+a8usrTlUXN5cb+G8cfOGDYADbezmAv8YB27aNw4ABvw+hwECAGvulIUDtOfb");
    AP(@"NmsAMGAnp+4cPQAIpPONAO88AAA77xSATjvarCMOABCMw44557zzTR931CNOPAdI0FM2");
    AP(@"8KhDTgEFrNNNO91IAIA75CgAATvY2LPNBAAI9c425byzhx/bfBMOAAqwEw86+7zzD5Ds");
    AP(@"qMOAewDMIw83c2UwDj3piIcbPtQgYgg38YwzVz3utDOOOtooAAA5FADADwAbsNMPOeF5");
    AP(@"l845/QDQwQXptONNIHnQA09WADBwzzv8SLDmAfAEKk6a9/xoJACIArAVpOtg808chJhj");
    AP(@"Dpj/AUAZAPnM5c6RAmRgYD8MzIUAOaHO9UE87bSRc04gs5RSTz36SPVPOfToA88/+MyD");
    AP(@"az7rUAWPO1LZMw8/UuWDzlb/gDJLN5Bo808+9NwD7D/zkOTPVf/Y888+9/y6jlS6onPt");
    AP(@"P+RAc8k4/hCDiDXTsLPNNuNAw0435OBLDW7XePNNOdHIg8016HDTTTngeNPNM3so848/");
    AP(@"uxLCx8UdYazxxX30sfHHF7tSzsQBAQA7");
    AP(@"\t</data>\n\t<key>upIconData</key>\n\t<data>\n");
    AP(@"R0lGODlhGQARAPcAAAAAAAEBAQICAgMDAwQEBAUFBQYGBgcHBwgICAkJCQoKCgsLCwwM");
    AP(@"DA0NDQ4ODg8PDxAQEBERERISEhMTExQUFBUVFRYWFhcXFxgYGBkZGRoaGhsbGxwcHB0d");
    AP(@"HR4eHh8fHyAgICEhISIiIiMjIyQkJCUlJSYmJicnJygoKCkpKSoqKisrKywsLC0tLS4u");
    AP(@"Li8vLzAwMDExMTIyMjMzMzQ0NDU1NTY2Njc3Nzg4ODk5OTo6Ojs7Ozw8PD09PT4+Pj8/");
    AP(@"P0BAQEFBQUJCQkNDQ0REREVFRUZGRkdHR0hISElJSUpKSktLS0xMTE1NTU5OTk9PT1BQ");
    AP(@"UFFRUVJSUlNTU1RUVFVVVVZWVldXV1hYWFlZWVpaWltbW1xcXF1dXV5eXl9fX2BgYGFh");
    AP(@"YWJiYmNjY2RkZGVlZWZmZmdnZ2hoaGlpaWpqamtra2xsbG1tbW5ubm9vb3BwcHFxcXJy");
    AP(@"cnNzc3R0dHV1dXZ2dnd3d3h4eHl5eXp6ent7e3x8fH19fX5+fn9/f4CAgIGBgYKCgoOD");
    AP(@"g4SEhIWFhYaGhoeHh4iIiImJiYqKiouLi4yMjI2NjY6Ojo+Pj5CQkJGRkZKSkpOTk5SU");
    AP(@"lJWVlZaWlpeXl5iYmJmZmZqampubm5ycnJ2dnZ6enp+fn6CgoKGhoaKioqOjo6SkpKWl");
    AP(@"paampqenp6ioqKmpqaqqqqurq6ysrK2tra6urq+vr7CwsLGxsbKysrOzs7S0tLW1tba2");
    AP(@"tre3t7i4uLm5ubq6uru7u7y8vL29vb6+vr+/v8DAwMHBwcLCwsPDw8TExMXFxcbGxsfH");
    AP(@"x8jIyMnJycrKysvLy8zMzM3Nzc7Ozs/Pz9DQ0NHR0dLS0tPT09TU1NXV1dbW1tfX19jY");
    AP(@"2NnZ2dra2tvb29zc3N3d3d7e3t/f3+Dg4OHh4eLi4uPj4+Tk5OXl5ebm5ufn5+jo6Onp");
    AP(@"6erq6uvr6+zs7O3t7e7u7u/v7/Dw8PHx8fLy8vPz8/T09PX19fb29vf39/j4+Pn5+fr6");
    AP(@"+vv7+/z8/P39/f7+/v///ywAAAAAGQARAAAI/wD//TtXiI/Bg3z6IFxIyJzAf874VDPn");
    AP(@"7Zw5c+fOefNG8WJHc9X4OBvI59zDkyhTDuxjrlWvkc6IOYvZrJmzYTaHzdQJsxerQrSI");
    AP(@"EasGAEC1o0WPKl06jBitgs6oITX67VvRjdWwbvR21JlCrhqvfvN2lSwAs2O5ejOI8WJR");
    AP(@"j2/PFQVg7q1Htm3t1qW7F6Pec3jfvSv6zt1gAIWLGlb8DiNbw4cFu2NMeDIAd+4uGmxs");
    AP(@"2DJmynMByJOX2R2fQt7czVs9L7S81UVPvnbnrRArXg5VPoz9UN4/c7xYmeNjbp5ugbwf");
    AP(@"zhtuMiJHjBvbXuSY8eK7byIfmiu4sPtBhYVM/gILCAA7");
    AP(@"\t</data>\n\t</dict>\n</plist>\n");
    CFStringRef errorString;
    NSData *xmlData = [NSData dataWithBytes:[XML_STRING cString] length:[XML_STRING length]];
    PLIST = (id)CFPropertyListCreateFromXMLData(kCFAllocatorDefault, 
                                (CFDataRef)xmlData,
                                0, 
                                &errorString);
}

void PutClassArtInDir(id path) {
    if (![MANAGER fileExistsAtPath:path]) [MANAGER createDirectoryAtPath:path attributes:nil];
    id classIconData = [PLIST objectForKey:@"classIconData"];
    [classIconData writeToFile:[path stringByAppendingPathComponent:@"class.gif"] atomically:YES];
    id upIconData = [PLIST objectForKey:@"upIconData"];
    [upIconData writeToFile:[path stringByAppendingPathComponent:@"up.gif"] atomically:YES];

}

void SetupBuffers() {
    INPUT_BUFFER = [NSMutableString stringWithCapacity:1 << 20];
    OUTPUT_BUFFER = [NSMutableData dataWithLength:1 << 21];
    RANGE_BUFFER = [NSMutableData dataWithLength:4096];
    CreatePlist();
    RESOURCE_DIRECTORY = @"/Developer/Documentation/Cocoa/Reference";
}

int main(int argc, char *argv[]) {
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
    MANAGER = [NSFileManager defaultManager];
    SetupBuffers();
    id currentDirectory = [MANAGER currentDirectoryPath];
    OUTPUT_DIRECTORY = [currentDirectory stringByAppendingPathComponent:@"Reference"];    
    id options = PullOptions(argc, argv);
    id columnNumberValue = [options objectForKey:@"c"];
    COLUMN_NUMBER = [columnNumberValue intValue];
    if (COLUMN_NUMBER == 0) COLUMN_NUMBER = 4;
    if ([MANAGER fileExistsAtPath:OUTPUT_DIRECTORY]) {
        NSLog(@"\n\nERROR: fileExistsAtPath: %@\nlaunch tool in empty directory", OUTPUT_DIRECTORY);
        return -1;
    }
    
    [MANAGER createDirectoryAtPath:OUTPUT_DIRECTORY attributes:nil];
    NSLog(@"\n\nSaveTheTrees:\n    output directory -> %@\n    column count: %i\n    options: %@", 
        OUTPUT_DIRECTORY, COLUMN_NUMBER, [options description]);
    if (CopyAndEditReferenceFiles()) NSLog(@"success!!");
    else {NSLog(@"failed!!"); return -1;}
    [pool release];
    return 0;
}



