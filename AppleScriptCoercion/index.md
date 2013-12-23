---
layout: page
title: AppleScriptCoercion
---



See also HowToSupportAppleScript. 

Many Cocoa developers who have tried to support custom object classes in AppleScript have run into the problem of coercion. Apple has a very vague class that seems like it would handle this for us, NSScriptCoercionHandler. NSScriptCoercionHandler lets you register handlers for your custom classes. The only problem with this class: it isn't called every where you would expect it to be used. It seems to only be used when "coerceValue:forKey:" (called when you set a property with a different class than KVC expects.) On the other side of things is the get command. You would expect it to use the same handlers to coerce to the requested class, but this is not the case.

I have subclasses NSGetCommand and tacked on to the performDefaultImplementation method the code required to use NSScriptCoercionHandler and some extra logic to do general coercion for common classes. To get this to work you will need to call this early in you application load sequence: [JVGetCommand poseAsClass:[NSGetCommand class]]. -- TimothyHatcher

    
   @implementation JVGetCommand
   - (id) performDefaultImplementation {
        id ret = [super performDefaultImplementation];

        NSAppleEventDescriptor *rtypDesc = self appleEvent] paramDescriptorForKeyword:'rtyp'];
        if( rtypDesc ) { // the get command is requesting a coercion (requested type) 
                [[NSScriptClassDescription *classDesc = [[NSScriptSuiteRegistry sharedScriptSuiteRegistry] classDescriptionWithAppleEventCode:[rtypDesc typeCodeValue]];
                Class class = NULL;

                if( classDesc ) { // found the requested type in the script suites.
                        class = NSClassFromString( [classDesc className] );
                } else { // catch some common types that don't have entries in the script suites.
                        switch( [rtypDesc typeCodeValue] ) {
                                case 'TEXT': class = [NSString class]; break;
                                case 'STXT': class = [NSTextStorage class]; break;
                                case 'nmbr': class = [NSNumber class]; break;
                                case 'reco': class = [NSDictionary class]; break;
                                case 'list': class = [NSArray class]; break;
                                case 'data': class = [NSData class]; break;
                        }
                }

                if( class && class != [ret class] ) {
                        id newRet = [[NSScriptCoercionHandler sharedCoercionHandler] coerceValue:ret toClass:class];
                        if( newRet ) return newRet;
                }

                // account for basic types that wont have a coercion handler but have common methods we can use.
                if( class == [NSString class] && [ret respondsToSelector:@selector( stringValue )] )
                        return [ret stringValue];
                else if( class == [NSString class] )
                        return [ret description];
                else if( [rtypDesc typeCodeValue] == 'long' && [ret respondsToSelector:@selector( intValue )] )
                        return [NSNumber numberWithLong:[ret intValue]];
                else if( [rtypDesc typeCodeValue] == 'sing' && [ret respondsToSelector:@selector( floatValue )] )
                        return [NSNumber numberWithFloat:[ret floatValue]];
                else if( ( [rtypDesc typeCodeValue] == 'doub' || [rtypDesc typeCodeValue] == 'nmbr' ) && [ret respondsToSelector:@selector( doubleValue )] )
                        return [NSNumber numberWithDouble:[ret doubleValue]];
        }

        return ret;
   }
   @end


The latest version of this implementation is also here: http://project.colloquy.info/trac/file/trunk/JVGetCommand.m (incase I make future modifications.) Comments and suggestions are welcome.

