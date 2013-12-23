---
layout: page
title: DictionaryToArrayTransformer
---



Here's a simpe value transformer that lets you bind dictionaries to array controllers. The dictionary is transformed into an array of dictionaries with the keys 'key' and 'value'.
From my understanding of bindings it will require "Handles Content As Compound Value" for mutable dictionaries, but other than that there's nothing to think about.

You are free to do as you wish with this code, and if your house burns down it's not my fault etc etc..

    

@interface DictionaryToArrayTransformer : NSValueTransformer {
}

@end
@implementation DictionaryToArrayTransformer

+ (Class)transformedValueClass {
    return [NSArray class];
}

+ (BOOL)allowsReverseTransformation {
    return YES;
}

- (id)transformedValue:(id)value {
    NSMutableArray *array = [NSMutableArray array];
    
    NSEnumerator *keyEnumerator = [value keyEnumerator];
    NSString *key = nil;
    while (key = [keyEnumerator nextObject]) {
        [array addObject:[NSDictionary dictionaryWithObjectsAndKeys:
            key, @"key",
            [value valueForKey:key], @"value", nil]];
    }
    
    return array;
}

- (id)reverseTransformedValue:(id)value {
    NSMutableDictionary *dictionary = [NSMutableDictionary dictionary];
    
    NSEnumerator *keyValuePairEnumerator = [value objectEnumerator];
    NSDictionary *keyValuePair = nil;
    while (keyValuePair = [keyValuePairEnumerator nextObject]) {
        [dictionary setValue:[keyValuePair valueForKey:@"value"]
                            forKey:[keyValuePair valueForKey:@"key"]];
    }
    
    return dictionary;
}

@end



Mr. Fisk


----

For many situations, this may be too simplistic.
See <http://www.cocoabuilder.com/archive/message/cocoa/2005/8/19/144601>.

----

Could anyone clarify when and why? From what I understand after reading that post, they merely achieve the same thing as using my transformer with the "Handle Content as Compound Value" binding option, only more efficient (the whole dictionary doesn't get rewritten on every modification).

