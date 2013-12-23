---
layout: page
title: CoreDataBindingsQuestion
---

I found this question on the CoreDataQuestions page, and it was right up front lacking an answer.  I also have the exact same question, and was hoping someone could shed light on the issue:

When you dial into an array through a key path, we have operators to do aggregation, for example like company.projects.@count or produc ts.@avg.prices .  When I try to use these on the MutableSets returned by CoreData relations, it can't give me a count, and the log reports that the @count key path is not supported.  What key path can I use if I want to know the number of objects on the other side of a to-many relation?  Or am I completely missing something?  It seems that a FetchedProperty might do this, but XCode rejects all of the predicates I try to build to do this.

*I got this to work by subclassing the "owner" of the to-many relation thus: *

    

+(void)initialize
{
	[MQUsage setKeys:[NSArray arrayWithObject:@"cues"]
             triggerChangeNotificationsForDependentKey:@"cueCount"];
       /* "cues" is the key to my related set */
	
	[super initialize];
		
}

-(unsigned int)cueCount
{
	return [[self valueForKey:@"cues"] count];
}



It seems like there should be a simple binding key for getting the cardinality of a to-many without having to write mehtods, but "_NSFaultingMutableSet" doesn't appear to be KVO compliant for -count.

