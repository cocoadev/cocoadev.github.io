---
layout: page
title: ToolbarItemIconOnNSPreferences
---



Hi folks,
I've successfully created a Preferences Pane for my Mac Address Book Plugin (files available at the bottom of this post).
Now I want to define the Icon show on the AB Preferences for my plugin, but I don't know how to do it (currently the AB icon is displayed). Can anyone point out how to do it?

The following code was based on http://www.cocoadev.com/index.pl?General/NSPreferences

General/NSPreferences.h

    
#import <Cocoa/Cocoa.h>

// Taken from /System/Library/Frameworks/General/AppKit.framework/General/AppKit

/*
 * Preferences are read from mainBundle's General/PreferencePanels.plist file:
 * Keys are
 *  General/ContentSize (string representation of a General/NSSize),
 *  General/UsesButtons (string with 0 or 1)
 *  General/PreferencePanels (array of dictionaries):
 *   Class (string)
 *   Identifier (string)
 */

#ifdef TIGER

@protocol General/NSPreferencesModule
- (id)viewForPreferenceNamed:(id)fp8;
- (id)imageForPreferenceNamed:(id)fp8;
- (BOOL)hasChangesPending;
- (void)saveChanges;
- (void)willBeDisplayed;
- (void)initializeFromDefaults;
- (void)didChange;
- (void)moduleWillBeRemoved;
- (void)moduleWasInstalled;
- (BOOL)moduleCanBeRemoved;
- (BOOL)preferencesWindowShouldClose;
@end

@interface General/NSPreferences : General/NSObject
{
    General/NSWindow *_preferencesPanel;
    General/NSBox *_preferenceBox;
    General/NSMatrix *_moduleMatrix;
    General/NSButtonCell *_okButton;
    General/NSButtonCell *_cancelButton;
    General/NSButtonCell *_applyButton;
    General/NSMutableArray *_preferenceTitles;
    General/NSMutableArray *_preferenceModules;
    General/NSMutableDictionary *_masterPreferenceViews;
    General/NSMutableDictionary *_currentSessionPreferenceViews;
    General/NSBox *_originalContentView;
    BOOL _isModal;
    float _constrainedWidth;
    id _currentModule;
    void *_reserved;
}

+ (id)sharedPreferences;
+ (void)setDefaultPreferencesClass:(Class)fp8;
+ (Class)defaultPreferencesClass;
- (id)init;
- (void)dealloc;
- (void)addPreferenceNamed:(id)fp8 owner:(id)fp12;
- (void)_setupToolbar;
- (void)_setupUI;
- (struct _NSSize)preferencesContentSize;
- (void)showPreferencesPanel;
- (void)showPreferencesPanelForOwner:(id)fp8;
- (int)showModalPreferencesPanelForOwner:(id)fp8;
- (int)showModalPreferencesPanel;
- (void)ok:(id)fp8;
- (void)cancel:(id)fp8;
- (void)apply:(id)fp8;
- (void)_selectModuleOwner:(id)fp8;
- (id)windowTitle;
- (void)confirmCloseSheetIsDone:(id)fp8 returnCode:(int)fp12 contextInfo:(void *)fp16;
- (BOOL)windowShouldClose:(id)fp8;
- (void)windowDidResize:(id)fp8;
- (struct _NSSize)windowWillResize:(id)fp8 toSize:(struct _NSSize)fp12;
- (BOOL)usesButtons;
- (id)_itemIdentifierForModule:(id)fp8;
- (void)toolbarItemClicked:(id)fp8;
- (id)toolbar:(id)fp8 itemForItemIdentifier:(id)fp12 willBeInsertedIntoToolbar:(BOOL)fp16;
- (id)toolbarDefaultItemIdentifiers:(id)fp8;
- (id)toolbarAllowedItemIdentifiers:(id)fp8;
- (id)toolbarSelectableItemIdentifiers:(id)fp8;

@end

@interface General/NSPreferencesModule : General/NSObject <General/NSPreferencesModule>
{
    General/NSBox *_preferencesView;
    struct _NSSize _minSize;
    BOOL _hasChanges;
    void *_reserved;
}

+ (id)sharedInstance;
- (void)dealloc;
- (void)finalize;
- (id)init;
- (id)preferencesNibName;
- (void)setPreferencesView:(id)fp8;
- (id)viewForPreferenceNamed:(id)fp8;
- (id)imageForPreferenceNamed:(id)fp8;
- (id)titleForIdentifier:(id)fp8;
- (BOOL)hasChangesPending;
- (void)saveChanges;
- (void)willBeDisplayed;
- (void)initializeFromDefaults;
- (void)didChange;
- (struct _NSSize)minSize;
- (void)setMinSize:(struct _NSSize)fp8;
- (void)moduleWillBeRemoved;
- (void)moduleWasInstalled;
- (BOOL)moduleCanBeRemoved;
- (BOOL)preferencesWindowShouldClose;
- (BOOL)isResizable;

@end

#else

@protocol General/NSPreferencesModule
- (char)preferencesWindowShouldClose;
- (char)moduleCanBeRemoved;
- (void)moduleWasInstalled;
- (void)moduleWillBeRemoved;
- (void)didChange;
- (void)initializeFromDefaults;
- (void)willBeDisplayed;
- (void)saveChanges;
- (char)hasChangesPending;
- imageForPreferenceNamed:fp8;
- viewForPreferenceNamed:fp8;
@end

@interface General/NSPreferences:General/NSObject
{
    General/NSWindow *_preferencesPanel;	// 4 = 0x4
    General/NSBox *_preferenceBox;	// 8 = 0x8
    General/NSMatrix *_moduleMatrix;	// 12 = 0xc
    General/NSButtonCell *_okButton;	// 16 = 0x10
    General/NSButtonCell *_cancelButton;	// 20 = 0x14
    General/NSButtonCell *_applyButton;	// 24 = 0x18
    General/NSMutableArray *_preferenceTitles;	// 28 = 0x1c
    General/NSMutableArray *_preferenceModules;	// 32 = 0x20
    General/NSMutableDictionary *_masterPreferenceViews;	// 36 = 0x24
    General/NSMutableDictionary *_currentSessionPreferenceViews;	// 40 = 0x28
    General/NSBox *_originalContentView;	// 44 = 0x2c
    char _isModal;	// 48 = 0x30
    float _constrainedWidth;	// 52 = 0x34
    id _currentModule;	// 56 = 0x38
    void *_reserved;	// 60 = 0x3c
}

+ sharedPreferences;
+ (void)setDefaultPreferencesClass:(Class)fp8;
+ (Class)defaultPreferencesClass;
- init;
- (void)dealloc;
- (void)addPreferenceNamed:fp8 owner:fp12;
- (void)_setupToolbar;
- (void)_setupUI;
- (struct _NSSize)preferencesContentSize;
- (void)showPreferencesPanel;
- (void)showPreferencesPanelForOwner:fp8;
- (int)showModalPreferencesPanelForOwner:fp8;
- (int)showModalPreferencesPanel;
- (void)ok:fp8;
- (void)cancel:fp8;
- (void)apply:fp8;
- (void)_selectModuleOwner:fp8;
- windowTitle;
- (void)confirmCloseSheetIsDone:fp8 returnCode:(int)fp12 contextInfo:(void *)fp16;
- (char)windowShouldClose:fp8;
- (void)windowDidResize:fp8;
- (struct _NSSize)windowWillResize:fp8 toSize:(struct _NSSize)fp12;
- (char)usesButtons;
- _itemIdentifierForModule:fp8;
- (void)toolbarItemClicked:fp8;
- toolbar:fp8 itemForItemIdentifier:fp12 willBeInsertedIntoToolbar:(BOOL)fp16;
- toolbarDefaultItemIdentifiers:fp8;
- toolbarAllowedItemIdentifiers:fp8;
- toolbarSelectableItemIdentifiers:fp8;

@end

@interface General/NSPreferencesModule:General/NSObject <General/NSPreferencesModule>
{
    General/NSBox *_preferencesView;	// 4 = 0x4
    struct _NSSize _minSize;	// 8 = 0x8
    char _hasChanges;	// 16 = 0x10
    void *_reserved;	// 20 = 0x14
}

+ sharedInstance;
- (void)dealloc;
- init;
- preferencesNibName;
- (void)setPreferencesView:fp8;
- viewForPreferenceNamed:fp8;
- imageForPreferenceNamed:fp8;
- titleForIdentifier:fp8;
- (char)hasChangesPending;
- (void)saveChanges;
- (void)willBeDisplayed;
- (void)initializeFromDefaults;
- (void)didChange;
- (struct _NSSize)minSize;
- (void)setMinSize:(struct _NSSize)fp8;
- (void)moduleWillBeRemoved;
- (void)moduleWasInstalled;
- (char)moduleCanBeRemoved;
- (char)preferencesWindowShouldClose;
- (char)isResizable;

@end

#endif


NSPreferences_AKAConnect.h

    
#import <General/NSPreferences.h>


@interface NSPreferences_AKAConnect : General/NSPreferences {

}

@end


NSPreferences_AKAConnect.m

    
#import "NSPreferences_AKAConnect.h"
#import "General/AKAConnectPreferences.h"

@implementation NSPreferences_AKAConnect

+ (void) load
{
    [NSPreferences_AKAConnect poseAsClass:General/[NSPreferences class]];
}

+ sharedPreferences
{
    static BOOL	added = NO;
    id			preferences = [super sharedPreferences];

    if(preferences != nil && !added){
		General/NSLog(@"... adding AKA Connect Prefs now...");
        added = YES;
        [preferences addPreferenceNamed:@"AKA Connect" owner:General/[AKAConnectPreferences sharedInstance]];
    }
    
    return preferences;
}

@end


General/AKAConnectPreferences.h

    
/* General/AKAConnectPreferences */
#import <General/NSPreferences.h>

#import <General/AppKit/General/AppKit.h>

#import <Cocoa/Cocoa.h>

@interface General/AKAConnectPreferences : General/NSPreferencesModule
{
    General/IBOutlet General/NSTextField *akaUserName;
    General/IBOutlet General/NSSecureTextField *akaUserPass;
	General/IBOutlet General/NSButton *akaUserForce;
	General/IBOutlet General/NSComboBox *akaUserConnMode;
}
//- (General/IBAction)savePreferences:(id)sender;
@end


General/AKAConnectPreferences.m

    
#import "General/AKAConnectPreferences.h"

@implementation General/AKAConnectPreferences

- (id)preferencesNibName {
	return @"General/AKAPreferences";
}

- (void)loadUserSettings {
	General/NSUserDefaults *us = General/[NSUserDefaults standardUserDefaults];
	
	General/NSLog(@"loadUserSettings was called...");
	
	if ([us valueForKey:@"General/AKAUserName"] != nil ) {
		[akaUserName setStringValue:[us valueForKey:@"General/AKAUserName"]];
	}
	if ([us valueForKey:@"General/AKAUserPass"] != nil) {
		[akaUserPass setStringValue:[us valueForKey:@"General/AKAUserPass"]];
	}
	if ([us valueForKey:@"General/AKAUserForce"] != nil) {
		[akaUserForce setStringValue:[us valueForKey:@"General/AKAUserForce"]];
	}
	if ([us valueForKey:@"General/AKAConnMode"] != nil) {
		[akaUserConnMode setStringValue:[us valueForKey:@"General/AKAConnMode"]];
	}
}

- (void)willBeDisplayed {

	[self loadUserSettings];

}

- (void)initializeFromDefaults {

	[self loadUserSettings];

}

-(void) saveChanges {
	General/NSLog(@"Save changes was called...");
	
	General/NSUserDefaults *us = General/[NSUserDefaults standardUserDefaults];
	
	[us setValue:[akaUserName stringValue] forKey:@"General/AKAUserName"];
	[us setValue:[akaUserPass stringValue] forKey:@"General/AKAUserPass"];
	[us setValue:[akaUserConnMode stringValue] forKey:@"General/AKAConnMode"];
	[us setValue:[akaUserForce stringValue] forKey:@"General/AKAUserForce"];
}

@end


----
I've never written one of these, but it looks to me like General/NSPreferencesModule's -imageForPreferenceNamed: would be a good place to start. Did you try that? -CS
