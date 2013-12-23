---
layout: page
title: NSSpeechRecognizer
---

Describe NSSpeechRecognizer here.

ok so I have this code for my application that creates a NSSpeechRecognizer.

    
NSSpeechRecognizer *listen;
NSArray *cmds = [NSArray arrayWithObjects:@"next song",@"last song",nil];
listen = [[NSSpeechRecognizer alloc] init];
[listen setCommands:cmds];
[listen setDelegate:self];
[listen setListensInForegroundOnly:NO];
[listen startListening];
[listen setBlocksOtherRecognizers:YES];


This is the method that runs when a command is recognized. Does anyone know a method that
will run if the command is not recognized or the speech recognizer cannot understand what is being said.

    
- (void)speechRecognizer:(NSSpeechRecognizer *)sender didRecognizeCommand:(id)aCmd {
    if ([(NSString *)aCmd isEqualToString:@"next song"]) {
		NSLog(@"next song");
		[self performSelector:@selector(NextSong:)];
    }
	
    if ([(NSString *)aCmd isEqualToString:@"last song"]) {
		NSLog(@"last song");
		[self performSelector:@selector(LastSong:)];
    }
}


Thanks

----

My guess would be 'no'. ;-) For two reasons. 1 - Anything it heard, it'd be analyzing and unless it's a match, it'd constantly be 'not matching' for all the background sounds it hears, which is terribly inefficient like Homer Simpson's "Everything's OK Alarm" which sounds every three seconds unless something's *not* okay. 2 - There are no methods in the instance, delegate, or notifications sections of the docs for NSSpeechRecognizer. ;-)

