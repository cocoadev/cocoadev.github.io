---
layout: page
title: OmniExpatExample
---

The Omni Group has a small library that can be downloaded that includes calls for the Expat XML library. 

The simplest way of working with this framework is to use this nice encapsulated call:

  #import <OmniBase/OmniBase.h>
  #import <OmniExpat/OmniExpat.h>
 
  NSString *xmlFile = [NSString stringWithContentsOfFile:[NSString stringWithCString:argv[1]]];
  NSDictionary *propertyList = [xmlFile propertyListFromXMLFormat];

This code returns the elements in a dictionary. This is quick and simple use of the Expat library. I used the code from this framework to get up to speed a bit quicker on some Objective-C stuff. The other reason I started with this framework was that many things I do (or want to do) revolve around XML, include data validation, object building, etc. Because I use the builder pattern a lot with my XML, I did not want to use the dictionary and went to the base. This code is the manual way of parsing XML files.

 // XmlHandler.h
 #import <Foundation/Foundation.h>
 #import <OmniFoundation/OmniFoundation.h>
 #import <OmniExpat/OmniExpat.h>
 #import <OmniExpat/xmlparse.h>
 
 @interface XmlHandler : NSObject 
 {
 }
 
 - (void)parseXml: (NSString*)content;
 
 @end
 
 
 // XmlHandler.m
 #import "XmlHandler.h"
 
 void handleStartElement(void *userData, const char *name, const char *'attributes);
 void handleEndElement(void *userData, const char *name);
 void handleCharacters(void *userData, const char *data, int len);
 
 
 @implementation XmlHandler
 
 - (void)parseXml: (NSString*)content
 {
     XML_Parser parser = XML_ParserCreate("UTF-8");
     XML_SetElementHandler(parser, handleStartElement, handleEndElement);
     XML_SetCharacterDataHandler(parser, handleCharacters);
     
     if (XML_Parse(parser, [content UTF8String], [content length], YES ) == 0)
     {
         int errorCode = XML_GetErrorCode(parser);
         int lineNumber = XML_GetCurrentLineNumber(parser);
         const char* errorDescription = XML_ErrorString(errorCode);
         NSLog(@"Parse Failure, code: %d, line: %d, description: %s", errorCode, lineNumber, errorDescription);
         
         XML_ParserFree(parser);
     }
     
     XML_ParserFree(parser);
 }
 
 void handleStartElement(void *userData, const char *name, const char *'attributes)
 {
     NSString* elementName = [NSString stringWithUTF8String:name];
     NSLog(@"Start> %@",elementName);
     
     if (*attributes) 
     {
         char *'attributeNameIndex;
         
         /* Step to next key/value pair */
         for (attributeNameIndex = attributes; *attributeNameIndex != NULL; attributeNameIndex += 2 )        
         {
             NSString *attributeName = [NSString stringWithCString:*attributeNameIndex];
             NSString *attributeValue = [NSString stringWithCString:*(attributeNameIndex + 1)];
             
             NSLog(@"Attribute> %@=%@",attributeName,attributeValue);
         }
     }
 }
 
 void handleEndElement(void *userData, const char *name)
 {
     NSString* elementName = [NSString stringWithUTF8String:name];
 }
 
 void handleCharacters(void *userData, const char *data, int len)
 {
     NSData *utf8Data = [NSData dataWithBytes:data length:len];
     NSString *dataString = [NSString alloc] initWithData:utf8Data encoding:NSUTF8StringEncoding] autorelease];
     
     NSLog(@"CData=%@",dataString);
 }
 
 @end

Here is some simple calling code.

     XmlHandler* handler = [[XmlHandler alloc]init];
     [handler parseXml:@"<Root>aaa</Root>"];
     [handler release];



There are other features, but this meets most needs.
-- [[BryanZarnett


----
I would recommend Apple's XML Services before OmniExpat. Seems just as simple, but more powerful -- TheoHultberg/Iconara

