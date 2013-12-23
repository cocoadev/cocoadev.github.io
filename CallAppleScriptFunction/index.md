---
layout: page
title: CallAppleScriptFunction
---

I'm hoping to load an AppleScript file (compiled or not) at a specific point in my program and then call a function therein (assuming that the author implemented it, which is a safe assumption to make). I've gotten up to the point of setting up the NSAppleScript, but my only options for running it are simply calling executeAndReturnError: or executeAppleEvent:...

I'm guessing that I have to use the latter, but I'm not sure how to form the AppleEvent, as all I know is the function name and its parameters. If this is the way I need to go, could someone explain or provide an example of passing an AppleScript file data type from an NSString path?

Your help is appreciated.

-- RyanGovostes

----

I don't believe this is supported in straight Cocoa. You'd have to either use Carbon, or NDAppleScriptObject which adds various     - (BOOL)executeSubroutineNamed:... methods

----

Or KFAppleScript Handler Additions.  That one's a little more lightweight. 

It's always worth looking over the ObjectLibrary.  

----

It is tedious but not difficult. I don't know how to call a function directly but it's easy to send an event to AppleScript and pass parameters to the event. In most cases, it's as good as calling a function. If you really need to call a function, then have the event handler call the function.

Essentially you send the script an AppleEvent with     executeAppleEvent:error:. The method accepts an NSAppleEventDescriptor which, in turn, can contain a reference to a list of parameters. The only point to remember is that you must fill the list of parameters with NSAppleEventDescriptor so if you have regular Cocoa objects (e.g. NSString) you must convert them to NSAppleEventDescriptor first.

Here's an example. Suppose you want to call the following script from Cocoa:

    on show_message(user_message)
   tell application "Finder"
      display dialog user_message
   end tell
end show_message

In Cocoa you would write (I had to adapt the code slightly before publishing, there might be a typo but the general idea is working):

    NSDictionary *errors = [NSDictionary dictionary];
NSString *path = [[NSBundle mainBundle] pathForResource:@"message" ofType:@"scpt"];
if(path)
{
   NSURL *url = [NSURL fileURLWithPath:path];
   if(url)
   {
      // load the script from a resource
      NSAppleScript *appleScript = [[NSAppleScript alloc] initWithContentsOfURL:url error:&errors];
      if(appleScript)
      {
         // create the first (and in this case only) parameter
         // note we can't pass an NSString (or any other object
         // for that matter) to AppleScript directly,
         // must convert to NSAppleEventDescriptor first
         NSAppleEventDescriptor *firstParameter = [NSAppleEventDescriptor descriptorWithString:@"my message"];
         // create and populate the list of parameters
         // note that the array starts at index 1
         NSAppleEventDescriptor *parameters = [NSAppleEventDescriptor listDescriptor];
         [parameters insertDescriptor:firstParameter atIndex:1];
         // create the AppleEvent target
         ProcessSerialNumber psn = { 0, kCurrentProcess };
         NSAppleEventDescriptor *target = [NSAppleEventDescriptor descriptorWithDescriptorType:typeProcessSerialNumber
                                                                  bytes:&psn
                                                                  length:sizeof(ProcessSerialNumber)];
         // create an NSAppleEventDescriptor with the method name
         // note that the name must be lowercase (even if it is uppercase in AppleScript)
         NSAppleEventDescriptor *handler = [NSAppleEventDescriptor descriptorWithString:[@"show_message" lowercaseString]];
         // last but not least, create the event for an AppleScript subroutine
         // set the method name and the list of parameters
         NSAppleEventDescriptor *event = [NSAppleEventDescriptor appleEventWithEventClass:kASAppleScriptSuite
                                                                 eventID:kASSubroutineEvent
                                                                 targetDescriptor:target
                                                                 returnID:kAutoGenerateReturnID
                                                                 transactionID:kAnyTransactionID];
         [event setParamDescriptor:handler forKeyword:keyASSubroutineName];
         [event setParamDescriptor:parameters forKeyword:keyDirectObject];
         // at last, call the event in AppleScript
         if(![appleScript executeAppleEvent:event error:&errors]);
            R<nowiki/>eportAppleScriptErrors(errors);
         [appleScript release];
      }
      else
         R<nowiki/>eportAppleScriptErrors(errors);
   }
}

As you can see, it's not difficult to pass parameters to AppleScript if you remember to convert your parameters into NSAppleEventDescriptor.
The code is repetitive so it is worth moving into a wrapper or library.

-- BenoitMarchal


----

You can also call handlers that you define in a Script Suite as commands. This gives you more control as a developer over what is allowed in the AppleScript and gives the end user a reference. This technique was done in the Colloquy chat client for script plugins. Below is the method used to call a script handler by it's AE code and arguments keysed by an AE code (in a NSString). This is only a snippet, some other things are required from the full file at:

http://project.colloquy.info/trac/file/trunk/Plug-Ins/AppleScript%20Support/JVAppleScriptChatPlugin.m 

Warning: this uses a few private APIs.

-- TimothyHatcher

    - (id) callScriptHandler:(unsigned long) handler withArguments:(NSDictionary *) arguments forSelector:(SEL) selector {
        if( ! _script ) return nil;

        int pid = [[NSProcessInfo processInfo] processIdentifier];
        NSAppleEventDescriptor *targetAddress = [NSAppleEventDescriptor descriptorWithDescriptorType:typeKernelProcessID bytes:&pid length:sizeof( pid )];
        NSAppleEventDescriptor *event = [NSAppleEventDescriptor appleEventWithEventClass:'cplG' eventID:handler targetDescriptor:targetAddress returnID:kAutoGenerateReturnID transactionID:kAnyTransactionID];

        NSEnumerator *enumerator = [arguments objectEnumerator];
        NSEnumerator *kenumerator = [arguments keyEnumerator];
        NSAppleEventDescriptor *descriptor = nil;
        NSString *key = nil;
        id value = nil;

        while( ( key = [kenumerator nextObject] ) && ( value = [enumerator nextObject] ) ) {
                NSScriptObjectSpecifier *specifier = nil;
                if( [value isKindOfClass:[NSScriptObjectSpecifier class]] ) specifier = value;
                else specifier = [value objectSpecifier];

                if( specifier ) descriptor = value objectSpecifier] _asDescriptor]; // custom object, use it's object specitier
                else descriptor = [[[[NSAEDescriptorTranslator sharedAEDescriptorTranslator] descriptorByTranslatingObject:value ofType:nil inSuite:nil];

                if( ! descriptor ) descriptor = [NSAppleEventDescriptor nullDescriptor];
                [event setDescriptor:descriptor forKeyword:[key fourCharCode]];
        }

        NSDictionary *error = nil;
        NSAppleEventDescriptor *result = [_script executeAppleEvent:event error:&error];

        if( error && ! result ) { // an error
                int code = error objectForKey:[[NSAppleScriptErrorNumber] intValue];
                if( code == errAEEventNotHandled || code == errAEHandlerNotFound ) {
                        [self doesNotRespondToSelector:selector]; // disable for future calls
                } else {
                        NSString *errorDesc = [error objectForKey:NSAppleScriptErrorMessage];
                        NSScriptCommandDescription *commandDesc = [[NSScriptSuiteRegistry sharedScriptSuiteRegistry] commandDescriptionWithAppleEventClass:'cplG' andAppleEventCode:handler];
                        NSString *scriptSuiteName = [[NSScriptSuiteRegistry sharedScriptSuiteRegistry] suiteForAppleEventCode:'cplG'];
                        NSTerminologyRegistry *term = [[[NSClassFromString( @"NSTerminologyRegistry" ) alloc] initWithSuiteName:scriptSuiteName bundle:[NSBundle mainBundle]] autorelease];
                        NSString *handlerName = term commandTerminologyDictionary:[commandDesc commandName objectForKey:@"Name"];
                        if( ! handlerName ) handlerName = @"unknown";
                        if( NSRunCriticalAlertPanel( NSLocalizedString( @"AppleScript Plugin Error", "AppleScript plugin error title" ), NSLocalizedString( @"The AppleScript plugin \"%@\" had an error while calling the \"%@\" handler.\n\n%@", "AppleScript plugin error message" ), nil, NSLocalizedString( @"Edit...", "edit button title" ), nil, [self scriptFilePath] lastPathComponent] stringByDeletingPathExtension], handlerName, errorDesc ) == [[NSCancelButton ) {
                                [[NSWorkspace sharedWorkspace] openFile:[self scriptFilePath]];
                        }
                }

                return [NSError errorWithDomain:NSOSStatusErrorDomain code:code userInfo:error];
        }

        if( [result descriptorType] == 'obj ' ) { // an object specifier result, evaluate it to the object
                NSScriptObjectSpecifier *specifier = [NSScriptObjectSpecifier _objectSpecifierFromDescriptor:result inCommandConstructionContext:nil];
                return [specifier objectsByEvaluatingSpecifier];
        }

        // a static result evaluate it to the proper object
        return [[NSAEDescriptorTranslator sharedAEDescriptorTranslator] objectByTranslatingDescriptor:result toType:nil inSuite:nil];
}

