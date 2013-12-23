---
layout: page
title: ApplicationInteruptPauseOrWaitForSoundToFinish
---

Hello, this is my first page I've made on this site (beside my name page SamM). I have run into an issue with my most recent (and only main) application. The application simply incements or decrements a count instance variable when the user presses the corisponding button. I have also added other features: apple remote support, counting down from a specified amount and counting up styles, preferences for defaults of all values and the feature I need help with an interval at which to play a selected sound (system sounds right now). 

My problem is that the sound that plays will play but if you hve the sound set to play at every count and hold the button or repeatedly press it fast some of the sounds are skipped while updating the count depending on how long the sound is.

     Here is the place where i want the sound to play and where i want the program to wait:

    
- (void)setCount:(int)newCount{
	
	if(count == newCount || newCount < 0 || ([style isEqualToString:@"Down From:"] && newCount > countDown)){
	}else{
		
		if(remainder(newCount, beepOn) == 0 && newCount != 0 && noBeep != YES){
			
[beepSound play]; //this is the sound i want the program to wait for Its just a system sound you can can see where its assigned below

		}else if(countDownSelected && newCount == 0 && noBeep != YES)
		{
			[finishedSound play];
		}else
		{
			noBeep = NO;	
		}
		count = newCount;
	}
}



Counter Files:

    
#import <Cocoa/Cocoa.h>


@interface Counter : NSObject {
	int count, maxCount, beepOn;
	int countDown;
	BOOL countDownSelected, countAtTop, noBeep;
	NSSound *finishedSound;
	NSSound *beepSound;
	NSString *style;
}
@property int count, maxCount, beepOn;
@property (copy) NSString *style;
@property BOOL countDownSelected;
@property (readonly) BOOL countAtTop, isResetable;
@property int countDown;
@property (copy) NSSound *beepSound;

-(void)resetCount;
-(void)runCount;
-(void)syncWithUserDefaults;

@end

----
    
#import "Counter.h"


@implementation Counter

@synthesize count, maxCount, beepOn, style, countDownSelected, countDown, countAtTop, beepSound;

+ (NSSet *)keyPathsForValuesAffectingCountAtTop {
    return [NSSet setWithObjects:@"count", @"countDown",@"style",
			nil];
}

+ (NSSet *)keyPathsForValuesAffectingIsResetable {
	return [NSSet setWithObjects:@"countAtTop", @"count", @"countDownSelected", nil];
}

/*+ (NSSet *)keyPathsForValuesAffectingCountFromSelect{
		
	return [NSSet setWithObjects:@"style", @"countDown", nil];
}*/


- init
{
	if (self = [super init])
	{
		NSUserDefaults *userDefaults = [NSUserDefaults standardUserDefaults];
		beepOn = [userDefaults doubleForKey:@"beepOn"];
		countDown = [userDefaults doubleForKey:@"countDown"];
		if(userDefaults stringForKey:@"style"] isEqualToString:@"Count Down"]){
			style = @"Down From:";
			countDownSelected = YES;
			count = countDown;
		}else{
			style = @"Up";
			countDownSelected = NO;
			count = 0;
		}
		noBeep = NO;
		//filepath is the location of the sound in the bundle
		//[[NSString *filePath = [[NSBundle mainBundle] pathForResource:@"Reward" ofType:@"mp3"];
		finishedSound = [NSSound soundNamed:@"Reward.mp3"];
		//[filePath release];
		
		[self addObserver:self 
			   forKeyPath:@"style" 
				  options:NSKeyValueObservingOptionNew 
				  context:NULL];
		[self addObserver:self 
			   forKeyPath:@"countDown" 
				  options:NSKeyValueObservingOptionNew 
				  context:NULL];
	}
	return self;	
}

- (void)setCount:(int)newCount{
	
	if(count == newCount || newCount < 0 || ([style isEqualToString:@"Down From:"] && newCount > countDown)){
	}else{
		
		if(remainder(newCount, beepOn) == 0 && newCount != 0 && noBeep != YES){
			[beepSound play];
		}else if(countDownSelected && newCount == 0 && noBeep != YES)
		{
			[finishedSound play];
		}else
		{
			noBeep = NO;	
		}
		count = newCount;
	}
}


- (void)resetCount{
	noBeep = YES;
	if(countDownSelected && self.isResetable){
		self.count = countDown;
	}else {
		self.count = 0;
	}
}

- (void)observeValueForKeyPath:(NSString *)keyPath
					  ofObject:(id)object
                        change:(NSDictionary *)change
                       context:(void *)context
{
    if ([keyPath isEqual:@"style"]) {
		if(change objectForKey:[[NSKeyValueChangeNewKey] isEqualToString:@"Down From:"]){
			noBeep = YES;
			self.count = countDown; 
			self.countDownSelected = YES;
		}else{
			noBeep = YES;
			self.count = 0;
			self.countDownSelected = NO;
		}
    }else if([keyPath isEqualToString:@"countDown"]){
		self.count = countDown;
	}else{
		
		[super observeValueForKeyPath:keyPath
							 ofObject:object
							   change:change
							  context:context];
	}
}

- (BOOL)countAtTop{
	if(countDownSelected && count==countDown){
		return YES;
	}else{
		return NO;
	}
}

- (void)runCount{
	(countDownSelected) ? self.count-- : self.count++;
}

- (BOOL)isResetable{
	return (self.countAtTop || (!countDownSelected && count ==0))? NO:YES;
}

- (void)syncWithUserDefaults{
	NSUserDefaults *userDefaults = [NSUserDefaults standardUserDefaults];
	self.beepOn = [userDefaults doubleForKey:@"beepOn"];
	self.countDown = [userDefaults doubleForKey:@"countDown"];
	if(userDefaults stringForKey:@"style"] isEqualToString:@"Count Down"]){
		self.style = @"Down From:";
	}else{
		self.style = @"Up";
	}
}

@end



----


Counter Controller Files:

    
#import <Cocoa/Cocoa.h>
#import "Counter.h"
#import "[[AppleRemote.h"
#import "RemoteControl.h"
#import "MaxCountTransformer.h"
#import "ZeroTransformer.h"



@interface CounterController : NSObject {
	IBOutlet NSButton *addButton;
	IBOutlet NSButton *subtractButton;
	Counter *counter;
	RemoteControl *remoteControl;
	NSArray *systemSoundNames;
	NSString *_selectedBeepSound;
	NSWindowController *_preferencesPanelController;
}

@property(copy) NSArray *systemSoundNames;
@property(copy) NSString *selectedBeepSound;

- (IBAction)addToCounter:(id)sender;
- (IBAction)subtractFromCounter:(id)sender;
- (IBAction)showPreferencesPanel:(id)sender;

@end

----
    
#import "CounterController.h"

static NSString *CounterAppBeepOnPreferenceKey = @"beepOn";
static NSString *CounterAppStylePreferenceKey = @"style";
static NSString *CounterAppCountDownPreferenceKey = @"countDown";
static NSString *CounterAppBeepSoundPreferenceKey = @"beepSound";

@implementation CounterController

@synthesize systemSoundNames, selectedBeepSound = _selectedBeepSound;



-(void)findSystemSoundNames:(id)inObject {
	NSFileManager *fileManager = [NSFileManager defaultManager];
	NSMutableArray *sounds = [NSMutableArray array];
	NSArray *foundItems = [fileManager contentsOfDirectoryAtPath:@"/System/Library/Sounds" error:NULL];
	foundItems = [foundItems pathsMatchingExtensions:[NSArray arrayWithObjects:@"aiff", @"aif",@"mp3",nil]];
	NSEnumerator *soundsEnum = [foundItems objectEnumerator];
	NSString *currSound = nil;
	while (currSound = [soundsEnum nextObject]) {
		[sounds addObject:[currSound stringByDeletingPathExtension]];
	}
	self.systemSoundNames = [NSArray arrayWithArray:sounds];
}

-(void)setSelectedBeepSound:(NSString *)selectedSound {
	NSSound *newSound = [NSSound soundNamed:selectedSound];
	[newSound play];
	counter.beepSound = [newSound copy];
	_selectedBeepSound = [selectedSound copy];
	[newSound release];
	NSLog(@"selectedSound: %@   newSound: %@  counter.beepSound: %@  _selectedBeepSound: %@", selectedSound, newSound, counter.beepSound, _selectedBeepSound);
}

@end



----

This is an exceedingly bad idea. Why not use QTKit and a basic queue? You get full control over play stop/start, you can be notified when the "movie" (sound) stops, and there's no need whatsoever to block the whole UI to wait for a series of sounds to play.

----

I believe that simply using NSSound and a couple of boolean setting made it work although if you click the button multiple times (not holding it down) you only get one increment up which isn't to much of a problem for me. Im not sure about using QTKit im not sure i need that kind of functionality the sounds that play are the system alerts and i dont think it would be worth going through the trouble right now but when i get more time i will look more into it i does seem quite appealing to have a queue and a notification of a stop. 

Also I was wondering if there is an easy way to share (preferably on this site) an application so that i could possibly let you guys see the whole application and use it to see exactly what i am talking about? I have another app that has a small problem that would be hard to put correctly into words. Thank You once again for your help. 

----

It's clear what you're talking about, but NSSound was not meant to work the way you want it to. "Simply using NSSound and a couple of boolean setting" won't work, period. The minimum required to do what you need is QTKit. Simply playing a sound is EXTREMELY lightweight. It's your only choice.

