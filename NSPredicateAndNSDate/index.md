---
layout: page
title: NSPredicateAndNSDate
---

I've been looking for examples and/or documentation on how to filter an NSArray through NSPredicate by using NSDate.  I have the data in a CoreData database with a "created" date parameter and would like to be able to filter by the date it was created.  Where can I find more information about this?

*Yeah, I have been having this same problem and would like to know the way to do it.* --LoganCollins

----

Easy (I think, I'm not looking at actual code but I think the stuff below works):

    
NSPredicate *p = [NSPredicate predicateWithFormat:@"created_key > %@", someDateAsNSDate]; // Grab anything after date created


----

Yeah, that's what I though when I was writing the code. It seems like the NSDate sends its description to be compared, which doesn't cope well with what CoreData sends for the key. They either should both send NSDate object or NSString descriptions, but I keep getting exceptions about can't compare dateWithTimeIntervalSinceReferenceDate and such, no matter which methods I try to compare it by.

When I try the code above, (which is what I had been using before), I get the following exception: "Exception raised during posting of notification.  Ignored.  exception: *** -[NSCFString timeIntervalSinceReferenceDate]: selector not recognized [self = 0x32f950]". It seems that the predicate is trying to compare an NSDate with and NSString, just as I said above. --Logan Collins

It does try to compare an NSDate with an NSString and thus fails. You need to build your NSPredicate programmatically rather than using a format. This works for me: 
    
NSPredicate *p = [NSComparisonPredicate predicateWithLeftExpression:[NSExpression expressionForKeyPath:@"created_key"] 
                                                    rightExpression:[NSExpression expressionForConstantValue:yourNSDateValue] 
                                                           modifier:NSDirectPredicateModifier 
                                                               type:NSGreaterThanPredicateOperatorType
                                                            options:0];

-- LeifSinger

----

Just in case you are using some string-based magic to create a larger query, this works as well:

    
NSString      *dateExpression = [[NSExpression expressionForConstantValue:yourNSDateValue]description];
NSPredicate *p = [NSPredicate predicateWithFormat:@"created_key > %@", dateExpression ]; // Grab anything after date created

-- Gerd Knops

Gerd, Leif,
neither of those solutions work, for me.
I am creating a spotlight query, and setting the predicate in code.
I am successful in every conceivable aspect of the implementation except for getting spotlight to compare NSDates and return values.

Currently, I create a predicate by doing this:

    
        NSString* theString = [[NSExpression expressionForConstantValue:theDate] description];
        
        NSMutableString* fro = [[NSMutableString alloc] init];
        NSPredicate* datePred;
       [fro appendString:@"kMDItemDueDate > "];
        [fro appendString:theString];

         datePred = [NSPredicate predicateWithFormat:fro];


but I'd tried it Leif's way as well. I don't get crashes, I just Never get any results.
this should not be this baffling, its a freaking date Object!

