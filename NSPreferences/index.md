---
layout: page
title: NSPreferences
---



NSPreferences is a class the provides a pseudo-standard look-and-feel for editing application preferences. It is used in both Safari and Mail.  It basically controls a tabbed view with a toolbar-style series of icons.  IMHO, it is much more efficient for small sets of preferences than the PreferencesPane interface seen in System Preferences.

This falls under the UndocumentedGoodness category.  There are a few minor hiccups, but after that, it's smooth sailing. http://goo.gl/OeSCu

Due to something funky in the NSPreferences init method, I've found that you have to do this override to avoid a segfault.  I'm kind of interested in figuring out why this is, but for the time being, it was easier to hack around.

    
@interface MSPreferences : NSPreferences
{
}

@end


@implementation MSPreferences

- init
{
	_preferenceTitles = [[NSMutableArray alloc] init];
	_preferenceModules = [[NSMutableArray alloc] init];
	_masterPreferenceViews = [[NSMutableDictionary alloc] init];
	_currentSessionPreferenceViews = [[NSMutableDictionary alloc] init];
	return self;
}

@end


What you mostly need to do is extend the NSPreferencesModule and flesh it out with all the gory details of your interface.

    
@interface NSPreferencesModule:NSObject <NSPreferencesModule>
{
    NSBox *_preferencesView;	// 4 = 0x4
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

@interface SamplePreferencesModule : NSPreferencesModule
{
}
@end

@implementation SamplePreferencesModule

/**
 * Image to display in the preferences toolbar
 */
- (NSImage *) imageForPreferenceNamed:(NSString *)_name

/**
 * Override to return the name of the relevant nib
 */
- (NSString *) preferencesNibName

- (void) didChange

- (NSView*) viewForPreferenceNamed:(NSString *)aName;

/**
* Called when switching preference panels.
 */
- (void) willBeDisplayed

/**
 * Called when window closes or "save" button is clicked.
 */
- (void) saveChanges

/**
* Not sure how useful this is, so far always seems to return YES.
 */
- (BOOL) hasChangesPending

/**
* Called when we relinquish ownership of the preferences panel.
 */
- (void)moduleWillBeRemoved

/**
* Called after willBeDisplayed, once we "own" the preferences panel.
 */
- (void)moduleWasInstalled

@end


You'll need to define a nib file (and specify it's name in the module class.  You probably want to create an NSPreferencesModule class in InterfaceBuilder and make sure it has an outlet for NSBox* _preferencesView.  Once you have that taken care of, you just need the following lines in you application:

    
	[NSPreferences setDefaultPreferencesClass:[MSPreferences class]];
	NSPreferences* prefs = [NSPreferences sharedPreferences];
	[prefs addPreferenceNamed:@"SamplePreferencesModule" owner:[SamplePreferencesModule sharedInstance]];


Obviously, I've left out a few things from my actual implementation, but hopefully this can get you started.  If you are having trouble, please drop me a note and I'll try update these notes to help you solve your problem.  --MikeSolomon

----

A more complete header for NSPreferences (and friends) that has 10.2 and 10.3 differences broken out.

    
#import <Foundation/NSObject.h>
#import <Foundation/NSGeometry.h>
#import <AppKit/NSNibDeclarations.h>

// Private classes from the AppKit framework; used by Safari and Mail.

@class NSWindow;
@class NSMatrix;
@class NSBox;
@class NSButtonCell;
@class NSImage;
@class NSView;
@class NSMutableArray;
@class NSMutableDictionary;

@protocol NSPreferencesModule
#if MAC_OS_X_VERSION_MAX_ALLOWED >= MAC_OS_X_VERSION_10_3
- (BOOL) preferencesWindowShouldClose;
- (BOOL) moduleCanBeRemoved;
- (void) moduleWasInstalled;
- (void) moduleWillBeRemoved;
#endif
- (void) didChange;
- (void) initializeFromDefaults;
- (void) willBeDisplayed;
- (void) saveChanges;
- (BOOL) hasChangesPending;
- (NSImage *) imageForPreferenceNamed:(NSString *) name;
- (NSView *) viewForPreferenceNamed:(NSString *) name;
@end

@interface NSPreferences : NSObject {
#if MAC_OS_X_VERSION_MAX_ALLOWED <= MAC_OS_X_VERSION_10_2
	id preferencesPanel;
	NSBox *preferenceBox;
	id moduleMatrix;
	id okButton;
	id cancelButton;
	id applyButton;
#else
	NSWindow *_preferencesPanel;
	NSBox *_preferenceBox;
	NSMatrix *_moduleMatrix;
	NSButtonCell *_okButton;
	NSButtonCell *_cancelButton;
	NSButtonCell *_applyButton;
#endif
	NSMutableArray *_preferenceTitles;
	NSMutableArray *_preferenceModules;
#if MAC_OS_X_VERSION_MAX_ALLOWED <= MAC_OS_X_VERSION_10_2
	NSMutableDictionary *_preferenceViews;
#else
	NSMutableDictionary *_masterPreferenceViews;
	NSMutableDictionary *_currentSessionPreferenceViews;
#endif
	NSBox *_originalContentView;
	BOOL _isModal;
#if MAC_OS_X_VERSION_MAX_ALLOWED >= MAC_OS_X_VERSION_10_3
	float _constrainedWidth;
	id _currentModule;
	void *_reserved;
#endif
}
+ (id) sharedPreferences;
+ (void) setDefaultPreferencesClass:(Class) class;
+ (Class) defaultPreferencesClass;

- (id) init;
- (void) dealloc;
- (void) addPreferenceNamed:(NSString *) name owner:(id) owner;

- (NSSize) preferencesContentSize;

- (void) showPreferencesPanel;
- (void) showPreferencesPanelForOwner:(id) owner;
- (int) showModalPreferencesPanelForOwner:(id) owner;
- (int) showModalPreferencesPanel;

- (void) ok:(id) sender;
- (void) cancel:(id) sender;
- (void) apply:(id) sender;

- (NSString *) windowTitle;
- (BOOL) usesButtons;

#if MAC_OS_X_VERSION_MAX_ALLOWED <= MAC_OS_X_VERSION_10_2
- (void) selectModule:(id) sender;
#endif
@end

@interface NSPreferencesModule : NSObject <NSPreferencesModule> {
#if MAC_OS_X_VERSION_MAX_ALLOWED <= MAC_OS_X_VERSION_10_2
	IBOutlet NSBox *preferencesView;
	BOOL hasChanges;
#else
	IBOutlet NSBox *_preferencesView;
	NSSize _minSize;
	BOOL _hasChanges;
	void *_reserved;
#endif
}
+ (id) sharedInstance;
- (id) init;
- (NSString *) preferencesNibName;
#if MAC_OS_X_VERSION_MAX_ALLOWED >= MAC_OS_X_VERSION_10_3
- (void) setPreferencesView:(NSView *) view;
#endif
- (NSView *) viewForPreferenceNamed:(NSString *) name;
- (NSImage *) imageForPreferenceNamed:(NSString *) name;
#if MAC_OS_X_VERSION_MAX_ALLOWED >= MAC_OS_X_VERSION_10_3
- (NSString *) titleForIdentifier:(NSString *) identifier;
#endif
- (BOOL) hasChangesPending;
- (void) saveChanges;
- (void) willBeDisplayed;
- (void) initializeFromDefaults;
- (void) didChange;
#if MAC_OS_X_VERSION_MAX_ALLOWED >= MAC_OS_X_VERSION_10_3
- (NSSize) minSize;
- (void) setMinSize:(NSSize) size;
- (void) moduleWillBeRemoved;
- (void) moduleWasInstalled;
- (BOOL) moduleCanBeRemoved;
- (BOOL) preferencesWindowShouldClose;
- (BOOL) isResizable;
#endif
@end


Also since _currentSessionPreferenceViews and _masterPreferenceViews were added in 10.3, leaving those allocs out in your custom override init method will allow your code to work in 10.2 and 10.3. NSPreferences seems to allocate these lazily. --TimothyHatcher

----

If you want to avoid stuff that isn't officially allowed for use by Apple (it looks like NSPreferences is private), you can also use my UKPrefsPanel class, which does something similar when combined with NSUserDefaults. Not quite as advanced, but similar enough. It's at http://www.zathras.de/programming/sourcecode.htm#UKPrefsPanel -- UliKusterer


----

Could someone post an example? I'm completely lost :(  



-JohnWells

----
I'm guessing there's a reason why sending init doesn't work.  You probably have to call something like initWithWindow: or something like that... for debugging purposes someone probably threw in an assertation or something like it into the init method which is why you see your program exit.

----

I'm using this to add a preferences module to Safaris preferences, for my plug-in. The only problem I've encountered, is that I have to click "Preferences..." from the Safari menu TWO times, before it show up. Every time. 
Do anyone know why this happens? And any idea how to fix it...?

UPDATE: Safari saves the last preference module that was open. If my module was not the last, it has to be clicked on in the preferences toolbar twice. If it was the last, it is as described above. It seems like it has to be activated one time first to "activate" it, and then another time to really open.

UPDATE2: It is fixed! I had connected the preferencesView-outlet to a NSView, but it needed to be a NSBox. The naming confused me. (and maybe others, so I don't delete this comment.)

-HannesPetrihttp://jamtangankanan.blogspot.com/
http://www.souvenirnikahku.com/
http://xamthonecentral.com/
http://www.jualsextoys.com
http://cupu.web.id
http://seoweblog.net
http://corsva.com
http://yudinet.com/
http://seo.corsva.com
http://seojek.edublogs.org/
http://tasgrosir-brandedmurah.com/
http://upin.blogetery.com/
http://www.symbian-kreatif.co.cc/
http://upin.blog.com/
http://release.pressku.com/
http://cupu.web.id/tablet-android-honeycomb-terbaik-murah/
http://cupu.web.id/hotel-murah-di-yogyakarta-klikhotel-com/
http://www.seoweblog.net/hotel-murah-di-semarang-klikhotel-com/

