---
layout: page
title: SimpleScripting
---

Apple's SimpleScripting project is located at

http://developer.apple.com/library/mac/#samplecode/SimpleScripting/Introduction/Intro.html

After downloading and building this sample application, you can use the ScriptEditor to access the single application-level property it supports: "ready".

    
   tell application "SimpleScripting"
      set isReady to ready
   end tell


The read-only ready property always returns a boolean "true" value.

The property is defined in the .sdef file with this code:

    
   <class name="application" code="capp" description="Our simple application class." inherits="application">
      <cocoa class="NSApplication"/>
      <property name="ready" code="Srdy" type="boolean" access="r" description="we're always ready"/>
   </class>


and the Objective-C code that implements the property:

SimpleApplication.h

    
   @interface NSApplication (SimpleApplication)
      - (NSNumber*) ready;
   @end


SimpleApplication.m

    
   @implementation NSApplication (SimpleApplication)
      - (NSNumber*) ready {
         return [NSNumber numberWithBool:YES];
      }
   @end


That's about all there is to this -- it really IS a simple scripting application. The ReadMe.txt file included in the sample code has more details on exactly what the application is doing. Please read it.


-----

HowToSupportAppleScript

