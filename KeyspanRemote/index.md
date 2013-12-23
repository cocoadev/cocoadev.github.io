---
layout: page
title: KeyspanRemote
---

Here's a simple class that lets your app respond to a Keyspan remote (URM - 15T).

    
@interface KeyspanObserver : NSObject {

}
@end

static NSDictionary *KeyspanActions = nil;
static BOOL ShouldRespondToKeyspanActionsInBackground = NO;

@implementation KeyspanObserver

static BOOL KeyspanObserverAction(SEL action, 
								  NSResponder *responder, 
								  NSNotification *note, 
								  NSMethodSignature *delegateSignature)
{
	if ([responder respondsToSelector:action]) {
		[responder performSelector:action withObject:note];
		return YES;
	}
	id delegate;				
	if ([responder respondsToSelector:@selector(delegate)] &&
		responder methodSignatureForSelector:@selector(delegate)] isEqual:delegateSignature] &&
		[(delegate = [responder performSelector:@selector(delegate)]) respondsToSelector:action])
	{
		[delegate performSelector:action withObject:note];
		return YES;
	}
	return NO;
}

+ ([[NSResponder *)firstResponder {
	NSResponder *firstResponder = [[NSApp keyWindow] firstResponder];
	NSArray *orderedWindows;
	if (!firstResponder && [(orderedWindows = [NSApp orderedWindows]) count])
		firstResponder = [(NSWindow *)[orderedWindows objectAtIndex:0] firstResponder];
	return firstResponder;
}

+ (void)keyspanMessage:(NSNotification *)note {

	if (![NSApp isActive] && !ShouldRespondToKeyspanActionsInBackground)
		return;

	NSString *message = note userInfo] objectForKey:@"message"];
	if ([message isKindOfClass:[[[NSString class]]) {
		SEL action = NSSelectorFromString([KeyspanActions objectForKey:message]);
		NSResponder *responder;		
		NSMethodSignature *sig = [NSWindow instanceMethodSignatureForSelector:@selector(delegate)];
		if (action) {
			BOOL didHandle = NO;
			for (responder = [self firstResponder]; 
				 responder && !(didHandle = KeyspanObserverAction(action, responder, note, sig)); 
				 responder = [responder nextResponder]);
			if (!didHandle) 
				KeyspanObserverAction(action, NSApp, note, sig);
		}
	}

}

+ (void)initialize {
	if (!KeyspanActions) {
		[[NSDistributedNotificationCenter defaultCenter] addObserver:self
															selector:@selector(keyspanMessage:)
																name:@"com.Keyspan.DMR.IAC.notification"
															  object:@"button"];		
		ShouldRespondToKeyspanActionsInBackground = 
			([[NSUserDefaults standardUserDefaults] objectForKey:@"ShouldRespondToKeyspanActionsInBackground"]) ? YES : NO;
		KeyspanActions = [[NSDictionary alloc] initWithObjectsAndKeys:
                                                    @"keyspanRemoteSelectButtonDown:", @"DWN SELECT",
                                                    @"keyspanRemoteSelectButtonUp:", @"UPP SELECT",
                                                    @"keyspanRemoteCycleButtonDown:", @"DWN CYCLE",
                                                    @"keyspanRemoteCycleButtonUp:", @"UPP CYCLE",
                                                    @"keyspanRemoteMuteButtonDown:", @"DWN MUTE",
                                                    @"keyspanRemoteMuteButtonUp:", @"UPP MUTE",
                                                    @"keyspanRemoteLeftArrowButtonDown:", @"DWN ARROW_LEFT",
                                                    @"keyspanRemoteLeftArrowButtonUp:", @"UPP ARROW_LEFT",
                                                    @"keyspanRemoteRightArrowButtonDown:", @"DWN ARROW_RIGHT",
                                                    @"keyspanRemoteRightArrowButtonUp:", @"UPP ARROW_RIGHT",
                                                    @"keyspanRemoteDownArrowButtonDown:", @"DWN ARROW_DN",
                                                    @"keyspanRemoteDownArrowButtonUp:", @"UPP ARROW_DN",
                                                    @"keyspanRemoteArrowButtonDown:", @"DWN ARROW_UP",
                                                    @"keyspanRemoteUpArrowButtonUp:", @"UPP ARROW_UP",
                                                    @"keyspanRemoteVolumeDownButtonDown:", @"DWN VOL_DN",
                                                    @"keyspanRemoteVolumeDownButtonUp:", @"UPP VOL_DN",
                                                    @"keyspanRemoteVolumeUpButtonDown:", @"DWN VOL_UP",
                                                    @"keyspanRemoteVolumeUpButtonUp:", @"UPP VOL_UP",
                                                    @"keyspanRemotePauseButtonDown:", @"DWN PAUSE",
                                                    @"keyspanRemotePauseButtonUp:", @"UPP PAUSE",
                                                    @"keyspanRemoteNextTrackButtonDown:", @"DWN NEXT_TRK",
                                                    @"keyspanRemoteNextTrackButtonUp:", @"UPP NEXT_TRK",
                                                    @"keyspanRemoteFastForwardButtonDown:", @"DWN FAST_FWD",
                                                    @"keyspanRemoteFastForwardButtonUp:", @"UPP FAST_FWD",
                                                    @"keyspanRemoteRewindButtonDown:", @"DWN REWIND",
                                                    @"keyspanRemoteRewindButtonUp:", @"UPP REWIND",
                                                    @"keyspanRemotePreviousTrackButtonDown:", @"DWN PREV_TRK",
                                                    @"keyspanRemotePreviousTrackButtonUp:", @"UPP PREV_TRK",
                                                    @"keyspanRemoteStopButtonDown:", @"DWN STOP",
                                                    @"keyspanRemoteStopButtonUp:", @"UPP STOP",
                                                    @"keyspanRemotePlayButtonDown:", @"DWN PLAY",
                                                    @"keyspanRemotePlayButtonUp:", @"UPP PLAY", nil];
	}
	[super initialize];
}
@end



The responder chain is searched first for an action handler. If the responder chain doesn't handle the action, NSApp and its delegate get a shot at it.

You can have a document object, an app delegate or a responder object handle remote button actions.

    

- (void)keyspanRemotePlayButtonUp:(NSNotification *)note {
	NSLog(@"<%p>%s:", self, __PRETTY_FUNCTION__);
}
- (void)keyspanRemotePlayButtonDown:(NSNotification *)note {
	NSLog(@"<%p>%s:", self, __PRETTY_FUNCTION__);
}

- (void)keyspanRemoteStopButtonUp:(NSNotification *)note {
	NSLog(@"<%p>%s:", self, __PRETTY_FUNCTION__);
}
- (void)keyspanRemoteStopButtonDown:(NSNotification *)note {
	NSLog(@"<%p>%s:", self, __PRETTY_FUNCTION__);
}



--zootbobbalu

