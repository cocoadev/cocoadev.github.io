---
layout: page
title: GraphicsServices
---

GraphicsServices.h:

    

#ifndef GRAPHICSSERVICES_H
#define GRAPHICSSERVICES_H

#import <CoreGraphics/CoreGraphics.h>

#ifdef __cplusplus
extern "C" {
#endif

// make sure to CFRelease(objectref) or [(id)objectref autorelease] the result of all GS...Create* methods to prevent leaking memory

// events

enum {
    kGSEventTypeOneFingerDown = 1,
    kGSEventTypeAllFingersUp = 2,
    kGSEventTypeOneFingerUp = 5,
    /* A "gesture" is either one finger dragging or two fingers down. */
    kGSEventTypeGesture = 6
} GSEventType;

/*struct __GSEvent;
typedef struct __GSEvent GSEvent;*/

struct GSPathPoint {
	char unk0;
	char unk1;
	short int status; // 3 when the mouse is down, I think there is a flag for moved too.
	int unk2;
	float x;
	float y;
};


typedef struct {
	int unk0; // like Delta Y - same value as fingerCount
	int unk1; 
	int type;
	int subtype;
	float avgX; //  sum of horizontal positions of all fingers / fingerCount ?
	float avgY; //  sum of vertical positions of all fingers / fingerCount ?
	float x;
	float y;
	int timestamp1;
	int timestamp2;
	int unk4;
	int modifierFlags;
	int eventCount; // mouse&gesture event count incl. every gestureChanged
	int unk6;
	int mouseEvent; // 1-MouseDown 2-MouseDragged 5-something like MouseUp 6-MouseUp
	short int dx;
	short int fingerCount; // number of fingers touching the screen.
	int unk7;
	int unk8;
	char unk9;
	char numPoints; // number of points in the points array. Can be > than fingerCount after a MouseUp.
	short int unk10;
	struct GSPathPoint points[10]; // contains the info on every point.
} GSEvent;

typedef GSEvent *GSEventRef;

int GSEventIsChordingHandEvent(GSEventRef ev); // 0-one finger down 1-two fingers down
int GSEventGetClickCount(GSEventRef ev); // seems to be always 1
CGRect GSEventGetLocationInWindow(GSEventRef ev); // the rect will have width and height during a swipe event
float GSEventGetDeltaX(GSEventRef ev); // number of fingers gesture started with
float GSEventGetDeltaY(GSEventRef ev); // actual fingerCount
CGPoint GSEventGetInnerMostPathPosition(GSEventRef ev); // position finger 1 ?
CGPoint GSEventGetOuterMostPathPosition(GSEventRef ev); // position finger 2 ?
unsigned int GSEventGetSubType(GSEventRef ev); // seems to be always 0
unsigned int GSEventGetType(GSEventRef ev);
int GSEventDeviceOrientation(GSEventRef ev);

void GSEventSetBacklightFactor(int factor);
void GSEventSetBacklightLevel(float level); // from 0.0 (off) to 1.0 (max)

// fonts

typedef enum {
	kGSFontTraitRegular = 0,
    kGSFontTraitItalic = 1,
    kGSFontTraitBold = 2,
    kGSFontTraitBoldItalic = (kGSFontTraitBold | kGSFontTraitItalic)
} GSFontTrait;

struct __GSFont;
typedef struct __GSFont *GSFontRef;

GSFontRef GSFontCreateWithName(char *name, GSFontTrait traits, float size);
char *GSFontGetFamilyName(GSFontRef font);
char *GSFontGetFullName(GSFontRef font);
BOOL GSFontIsBold(GSFontRef font);
BOOL GSFontIsFixedPitch(GSFontRef font);
GSFontTrait GSFontGetTraits(GSFontRef font);

// colors

CGColorRef GSColorCreate(CGColorSpaceRef colorspace, const float components[]);
CGColorRef GSColorCreateBlendedColorWithFraction(CGColorRef color, CGColorRef blendedColor, float fraction);
CGColorRef GSColorCreateColorWithDeviceRGBA(float red, float green, float blue, float alpha);
CGColorRef GSColorCreateWithDeviceWhite(float white, float alpha);
CGColorRef GSColorCreateHighlightWithLevel(CGColorRef originalColor, float highlightLevel);
CGColorRef GSColorCreateShadowWithLevel(CGColorRef originalColor, float shadowLevel);

float GSColorGetRedComponent(CGColorRef color);
float GSColorGetGreenComponent(CGColorRef color);
float GSColorGetBlueComponent(CGColorRef color);
float GSColorGetAlphaComponent(CGColorRef color);
const float *GSColorGetRGBAComponents(CGColorRef color);

// sets the color for the current context given by UICurrentContext()
void GSColorSetColor(CGColorRef color);
void GSColorSetSystemColor(CGColorRef color);

#ifdef __cplusplus
}
#endif

#endif



---- 

I mucked a bit with this trying to get event injection working (without success, so far) via -[UIApplication sendEvent:].

So here's what I found under OS version 3.1.2:

    
/*
	device:
	unk0:
		bit 17 (0x00020000) on when touchesBegan/moved, off on ended
		bit 16 (0x00010000) unknown, maybe signals short tap?
		others (0x000_0_0_) constant 0
		others (0x____FF__) touch id, starting at 2 when less than 5 touches, starting at 1 when 5 touches
		others (0x______0F) marks something about how/where touch first occured?
	sim:
	unk0:
		bit 17 same as device
		bit 16 !bit16
		0xFF______ apparently "source", 0x43,0x42 mouse scroll (0x00 on begin/end), 0xBF mouseDown, 0xFF mouseDrag/up
		0x______02 constant
		0x____02__ constant
*/

struct GSTouchPoint {
	uint32_t unk0; // some flags? different on sim and device
	float unk1; // 0.0 (device) 1.0 (sim)
	float touchSize; // 1.0 (sim), touch size on device
	float x;
	float y;
	UIWindow* window; // window where event occured?
};

typedef struct GSTouchPoint* GSTouchPointRef;

#define kGSEventTouchesBegan   1
#define kGSEventTouchesMoved   2
#define kGSEventTouchesEnded   6

@interface GSEvent : NSObject
{
//	unsigned int isa;
@public
	uint32_t type0;		// 0x02010180
	uint32_t type1;		// 0x00000BB9
	uint32_t r3;		// 0x00000000
	float avgX0;		// window or view pos?
	float avgY0;		
	float avgX1;		// view or window pos?
	float avgY1;
	uint32_t processId; // contains PID(?) on touchBegan
	uint64_t timestamp;	// contains some timesamp in ns
	UIWindow* window;	// event window
	uint32_t r11;		// 0x00000000
	uint32_t type12;	// 0x00000018 (device) 0x00007284 (sim)
	uint32_t gesture13;	// 3C(0011 1100) = 1 finger, 54(0101 0100) = 2 fingers, 6C(0110 1100) = 3 fingers, 84(1000 0100) = 4 fingers, 9C(1001 1100) = 5 fingers, 24(0010 0100) = 6 fingers/cancel
	uint32_t gesture14; // gesture? 1 = touchBegan, 2 = touchMoved, 6 = touchEnded, 5 = additionalTap in move
	uint16_t numInitialTouches; // number of initial touches
	uint16_t numCurrentTouches; // number of current touches
	uint32_t r16;		// 0x00000000
	uint32_t r17;		// 0x00000000
	uint32_t r18;		// 0x00000000
	uint32_t r19;		// 0x00000000
	uint32_t r20;		// 0x00000000
	uint32_t r21;		// 0x00000000
	uint8_t	 r22_0;		// 0x00
	uint8_t numPoints;
	uint16_t r22_2;		// 0x0000
//	uint32_t r22;
//	uint32_t r23;
	struct GSTouchPoint points[10]; // contains the info on every point.
}

@end

typedef GSEvent* GSEventRef;



Note, that, in reality, GSEvent's isa is NSCFType, but when creating one, using a proper Obj-C object seems easier, and for the purposes of accessing the values in an existing GSEvent is equivalent.

