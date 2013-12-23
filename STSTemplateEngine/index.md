---
layout: page
title: STSTemplateEngine
---

**STSTemplateEngine** is a universal template engine in Objective-C/Cocoa for embedding into Cocoa applications.

It features its own macro language for conditional template expansion and it supports unicode.

[ The links did point to sunrise-tel.com, which is a porn link site last time I clicked this link.  sunrisetel.net seems to get to the right place, so I've modified the links.  Note that the DNS change appears recent since at the time of writing some links on sunrisetel.net still point to the changed sunrise-tel.com.  -- JohnAllsup ]

Available for download from Sunrise Telephone Systems Ltd at http://www.sunrisetel.net/downloads/STSTemplateEngine.zip

----

**Licensing**
The STS Template Engine is licensed under the GNU General Public License Version 2. For projects and software products for which the terms of the GPL license are not suitable, alternative licensing terms are available from Sunrise Telephone Systems Ltd upon request.

**Prerequisites**
The STS Template Engine will work with MacOS X version 10.3.x "Panther" and MacOS X version 10.4.x "Tiger". It uses the Cocoa framework and does not require any other frameworks or libraries. With a bit of effort it could be adapted to work with earlier versions of MacOS X or with GNUstep.

**Documentation** is available at http://www.sunrisetel.net/software/devtools/STSTemplateEngine.shtml

----

**Overview**
The STS Template Engine is implemented as a category of NSString and it provides four additional class methods which return NSString objects by expanding templates. Two of the provided methods create NSString objects from templates passed as NSString objects and the other two methods create NSString objects from template files passed as POSIX pathnames. Template files can be in any encoding supported by the installed system software.

**Methods provided are:**

**stringByExpandingTemplate:usingDictionary:errorsReturned:**

    + (id)stringByExpandingTemplate:(NSString *)templateString
                usingDictionary:(NSDictionary *)dictionary
                 errorsReturned:(NSArray **)errorLog;


**stringByExpandingTemplate:withStartTag:andEndTag:usingDictionary:errorsReturned:**

    + (id)stringByExpandingTemplate:(NSString *)templateString
                   withStartTag:(NSString *)startTag
                      andEndTag:(NSString *)endTag
                usingDictionary:(NSDictionary *)dictionary
                 errorsReturned:(NSArray **)errorLog;


**stringByExpandingTemplateAtPath:usingDictionary:encoding:errorsReturned:**

    + (id)stringByExpandingTemplateAtPath:(NSString *)path
                      usingDictionary:(NSDictionary *)dictionary
                             encoding:(NSStringEncoding)enc
                       errorsReturned:(NSArray **)errorLog;


**stringByExpandingTemplateAtPath:withStartTag:andEndTag:usingDictionary:encoding:errorsReturned:**

    + (id)stringByExpandingTemplateAtPath:(NSString *)path
                         withStartTag:(NSString *)startTag
                            andEndTag:(NSString *)endTag
                      usingDictionary:(NSDictionary *)dictionary
                             encoding:(NSStringEncoding)enc
                       errorsReturned:(NSArray **)errorLog;


**Macros provided are:**

%IF, %IFNOT, %IFEQ, %IFNEQ, %IFDEF, %IFNDEF, %ELSIF, %ELSIFNOT, %ELSIFEQ, %ELSIFNEQ, %ELSIFDEF, %ELSIFNDEF, %ELSE, %ENDIF, %DEFINE, %UNDEF, %LOG, %ECHO and %DEBUG.

**Predefined placeholders are:**

On MacOS X 10.3 and later: _timestamp, _uniqueID, _hostname

On MacOS X 10.4 and later: _userContryCode, _userLanguage, _systemCountryCode, _systemLanguage

In addition to predefined placeholders, any number of user defined placeholders can be passed in an NSDictionary or entered into the template using %DEFINE.

----

Detailed documentation is available at http://www.sunrisetel.net/software/devtools/STSTemplateEngine.shtml

----

A less sophisticated, but somewhat more freely licensed, alternative is Buzz Andersen's NSString+Templating category, available at http://weblog.scifihifi.com/2003/10/22/nsstringtemplating .  It's distributed under the Creative Commons Attribution license.

----

If the GPL terms don't suit you, email us. The reason why this is GPL is simply that the space in which we are developing (open source telephony) is dominated by Linux developers and GPL dual licensing. We release the stuff that is of most interest in that space under GPL too, so we have something to bargain with. Some Linux developer likes our stuff for a commercial version - we like their stuff for a commercial version, et voila, an arrangement can be made.

That doesn't mean that MacOS X folks have to be shut out. If you are working for an organisation that can afford it, of course we'd expect a sponsorship or donation. On the other hand if you are a small shareware developer and you have a project we like, we're happy to grant a closed source license against attribution or a web banner or whatever is mutually agreeable. We're reasonable folks ;-)

Sunrise

