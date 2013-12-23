---
layout: page
title: TypeIDDoesNotCastToTypeBOOL
---

I just had a fun bug that caught me totally off gaurd. I know many cocoa programmers use the following condition throughout their code:

    
    id someObject = [someDictionary objectForKey:@"keyForAnObject"];
    if (someObject) {
       [self doSomethingWithObject:someObject];
    }
 

It is safe to use an object as a conditional value, but I got careless and assumed that type ID will cast to type BOOL. I don't know how often I've done this, but I'm going to check all my source now that I'm aware of this possible bug source. Here's where you will get in trouble if you cast type ID to type BOOL:

    
- (BOOL)continueWithIdentifier:(id)identifier {
    return (BOOL)[dictionaryInstanceVariable objectForKey:identifier];
}


A return value of type BOOL is only 8 bits wide, so if the 8 least significant bits of an address are all off then this address will cast to nil. For example if the address is 0x0188fa00, this address will represent a NO value. A conditional or bitwise operator has to be used if your return value is based on the existance of an object. The code example above should look like this:

    
- (BOOL)continueWithIdentifier:(id)identifier {
    return ([dictionaryInstanceVariable objectForKey:identifier]) ? YES : NO;
}


Or, like this:
    
- (BOOL)continueWithIdentifier:(id)identifier {
    return (nil != [dictionaryInstanceVariable objectForKey:identifier]);
}


----

And for you fancy pants who are thinking of doing something like this:

    
- (BOOL)continueWithIdentifier:(id)identifier {
    id someObject = [dictionaryInstanceVariable objectForKey:identifier];
    return (BOOL)(int)someObject;
}


I have one word for you: DON'T! This is the first step towards serious type abuse. Instead use one of the excellent alternatives provided above.

-- General/MikeTrent
