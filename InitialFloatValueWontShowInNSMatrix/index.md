---
layout: page
title: InitialFloatValueWontShowInNSMatrix
---



When I start my application, the text boxes in my NSMatrix don't show their values. 

Here's the relevant code from my view: 

    
@interface FOVview : NSView {

IBOutlet NSSlider *graySlider;
IBOutlet NSSlider *transparencySlider;
IBOutlet NSTextField *grayText;
IBOutlet NSTextField *transparencyText;
IBOutlet NSMatrix *sensorAngles;
IBOutlet NSMatrix *sensorY;
float grayness;
float transparency;
float angles[N_SENSORS];
float y[N_SENSORS];
float scale;

}

- (IBAction)changeSensor:(id)sender;
- (IBAction)changeColor:(id)sender;
- (IBAction)changeTransparency:(id)sender;
- (void)drawFanAt:(NSPoint)sensorLocation range:(float)r beamwidth:(float)bw angle:(float)a;
- (IBAction)copy:(id)sender;
- (IBAction)print:(id)sender;

@end

- (id)initWithFrame:(NSRect)frameRect
{
	if ((self = [super initWithFrame:frameRect]) != nil) {
		NSLog(@"FOVview: initWithFrame\n");
		grayness = 0.25;
		[graySlider setFloatValue:grayness];
		[grayText setFloatValue:grayness];
		transparency = 0.25;
		[transparencySlider setFloatValue:transparency];
		[transparencyText setFloatValue:transparency];
		scale = 96.0/2; // turn 2 m into 96 pixels (1 in)
		y[0] = 0.25; angles[0] = 25.0;
		y[1] = 1.25; angles[1] =-25.0;
		y[2] = 2.25; angles[2] = 25.0;
		y[3] = 3.25; angles[3] =-25.0;
		y[4] = 3.75; angles[4] = 25.0;
		y[5] = 4.75; angles[5] =-25.0;
		y[6] = 5.75; angles[6] = 25.0;
		y[7] = 6.75; angles[7] =-25.0;
		int i;
		for (i=0; i<N_SENSORS; i++) {
			sensorAngles cellWithTag:i] setFloatValue:angles[i;
			sensorY cellWithTag:i] setFloatValue:y[i;
		}
	}
	return self;
}


The     grayText and     transparencyText `NSTextView`s show their values when the window opens, but the     NSMatrixes don't.  From the documentation, it seems like this should work, and I can't find any other likely methods. 

Thanks!

----
You need to use AwakeFromNib.
----
Worked like a charm! Thanks.

