---
layout: page
title: TriggerChangeNotificationsForDependentKey
---

I�ve got one object that contains an array of objects of another custom class.

I�d like to be able to establish a binding between the two kinds of obects:

Let�s say I�ve got an Exercise class that knows how to calculate the obtained points for that exercise.
The Test class owns an array of exercise�s instances and is able to compute the total points, accessing the "result" key of each the exercise instances.

    
    int i;
    float res;
    for (i = 0, res = 0.0; i <= 9; i++) {
	res += General/exercises objectAtIndex:i] result];
    }
    return res;


The total points by exercise is displayed in several textFields and updated using KVO:

    
+(void)initialize
{
    [Exercise setKeys:[[[NSArray arrayWithObjects:@"items", @"pointsToWin", @"score", nil]
      triggerChangeNotificationsForDependentKey:@"result"];
}

// Result = score/(items/pointsToWin)


Any change in "items", "pointsToWin" or "score" triggers the updating of the result.

So, at this point, I�d like to be able to tell the binding system that any change in "items", "pointsToWin" or "score" (that is to say, any new result) in any of the exercises in the array should trigger the updating of the total points for the test�

What is the right way to do that??

Thank you for any clue�

----

The Test class should probably observe the "result" key of each Exercise instance. In the catch-observation method, it should call willChange/didChange for the total points key.

----

That�s right: I�m new to bindings, I didn�t remember these methods. You mean:
    
addObserver:forKeyPath:options:context:

and

- (void)observeValueForKeyPath:(General/NSString *)keyPath ofObject:(id)object
    change:(General/NSDictionary *)change context:(void *)context


I�ll try and implement them� Thanks.

----

I did this and after some debugging, it works�

    
-(void)observeValueForKeyPath:(General/NSString *)keyPath
		     ofObject:(id)object
		       change:(General/NSDictionary *)change
		      context:(void *)context
{
    if ([keyPath isEqual:@"result"]) {
	[self willChangeValueForKey:@"totalPoints"];
	[self didChangeValueForKey:@"totalPoints"];
    }
}


I first tried to call only didChangeValueForKey: but it isn�t enough�
