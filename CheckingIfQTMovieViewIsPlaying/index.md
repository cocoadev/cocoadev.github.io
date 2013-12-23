---
layout: page
title: CheckingIfQTMovieViewIsPlaying
---



How do I check if QTKit is finished playing? I tried Apple's code from the docs, provided below, but it won't compile and gives an error (Parse error before ==).

    
-(BOOL)isPlaying:(QTMovie *)aMovie
{
if ([aMovie rate == 0)
{
return NO;
}
return YES:
}


----

Look, at least a little bit, at your code before you paste it here. Take a five minute break. Go outside. Look at the clouds. Then come back in and realize that you're missing a closing ] in your message send.

----

That's not the problem - I fixed that and I still get the error.

----

Isn't this supposed to be impemented in the subclass, or just sending/receiving the messages to the movie? This probably wouldn't work in a controller, which sounds like what you're trying to do. Correct me if I'm wrong. -- JasonTerhorst

----

You fixed your closing ] and you still get "parse error before =="? I don't believe you. Post the code you're using now.

----

I'd consider this code to be more correct.. the biggest problem with the code above is that if aMovie is nil then [aMovie rate] is undefined (see MessagingNil).

    

-(BOOL)isPlaying:(QTMovie *)aMovie
{
return (aMovie != nil ? [aMovie rate] > 0.0 : NO);
}



this won't work with streaming movies though...any ideas on how to determine if your streaming movie has stopped?

Try this --Hasani Hunter

    

NSNotificationCenter *notificationCenter = [ NSNotificationCenter defaultCenter ];
		
[ notificationCenter addObserver:self selector:@selector(playNextMovieInList:) name:QTMovieDidEndNotification object:nil ];


