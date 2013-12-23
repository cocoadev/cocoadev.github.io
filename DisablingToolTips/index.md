---
layout: page
title: DisablingToolTips
---



It is possible and often desirable to allow the user to globally disable display of all ToolTips in an application.� It requires swizzling a method of a private AppKit class, but this is implemented as a "safe hack" which won't break your app if Apple changes it someday.� It currently works on all Mac OS X versions thru 10.5 Leopard:

1.� Place an item in the app's Help menu named "View Tool Tips", and set its FirstResponder action to toggleToolTips:

2.� In your app/delegate (or default responder), add these code snippets:

    

- (IBAction)toggleToolTips:(id)sender;

#import "SKWToolTipManager.h"

- (void)toggleToolTips:(id)sender
{
� � [NSApp setToolTipsEnabled:![NSApp toolTipsEnabled]];
}

- (BOOL)validateMenuItem:(id <NSMenuItem>)item
{
� � SEL action = [item action];
�
� � if (action == @selector(toggleToolTips:)) {
� � � � [item setState:([NSApp toolTipsEnabled] ? NSOnState : NSOffState)];
� � � � return [NSApp canDisableToolTips];
� � }

� � // etc...
}


3.� Create and add these two files to your project:

    
//� SKWToolTipManager.h
//
//� Created by Shaun Wexler on 6/21/04.
//� Copyright (c) 2004 SKW Development. All rights reserved.

#import <Cocoa/Cocoa.h>

@interface NSApplication (SKWToolTipManager)

- (void)setToolTipsEnabled:(BOOL)enabled;
- (BOOL)toolTipsEnabled;
- (BOOL)canDisableToolTips;

@end


    
//� SKWToolTipManager.m
//
//� Created by Shaun Wexler on 6/21/04.
//� Copyright (c) 2004 SKW Development. All rights reserved.

#import "SKWToolTipManager.h"
#import <objc/objc-runtime.h>

@implementation NSApplication (SKWToolTipManager)

static IMP displayToolTip = NULL;
static BOOL toolTipsDisabled = NO;
static BOOL canDisableToolTips = NO;

static IMP SKWRetargetInstanceMethod(Class originalClass, SEL originalSelector, Class targetClass, SEL targetSelector)
{
� � Method methodA = class_getInstanceMethod(originalClass, originalSelector);
� � Method methodB = class_getInstanceMethod(targetClass, targetSelector);
	
#if __OBJC2__
� � if (methodA && methodB && strcmp(method_getTypeEncoding(methodA), method_getTypeEncoding(methodB)) == 0) {
� � � � IMP impA = method_getImplementation(methodA);
� � � � method_setImplementation(methodA, method_getImplementation(methodB));
� � � � return impA;
� � }
#else // __OBJC__	
� � if (methodA && methodB && strcmp(methodA->method_types, methodB->method_types) == 0) {
� � � � IMP impA = methodA->method_imp;
� � � � methodA->method_imp = methodB->method_imp;
� � � � return impA;
� � }
#endif
� � return NULL;
}

+ (void)load
{
� � Class privateClass;
� � if (displayToolTip == NULL &&�(privateClass = NSClassFromString(@"NSToolTipManager"))) {
� � � ��displayToolTip = SKWRetargetInstanceMethod(privateClass, @selector(displayToolTip:), self, @selector(displayToolTipIfGloballyEnabled:));
� � }	
� � canDisableToolTips = displayToolTip != NULL;
� � toolTipsDisabled = canDisableToolTips && [[NSUserDefaults standardUserDefaults] boolForKey:@"SKWToolTipsDisabled"];
}

- (void)displayToolTipIfGloballyEnabled:(id)toolTip
{
� � if (!toolTipsDisabled && displayToolTip) {
� � � � (*displayToolTip)(self, _cmd, toolTip);
� � }
}

- (void)setToolTipsEnabled:(BOOL)enabled
{
� � toolTipsDisabled = !enabled && canDisableToolTips;
� � [[NSUserDefaults standardUserDefaults] setBool:toolTipsDisabled forKey:@"SKWToolTipsDisabled"];
}

- (BOOL)toolTipsEnabled
{
� � return !toolTipsDisabled;
}

- (BOOL)canDisableToolTips
{
� � return canDisableToolTips;
}

@end


4. Enjoy!� -~ SKW

ShaunWexler

----

