---
layout: page
title: SystemPreferences
---

http://www.diggory.net/grazing/General/SystemPrefsIcon.jpg

http://developer.apple.com/documentation/General/UserExperience/Conceptual/General/PreferencePanes/index.html
----

General/SystemPreferences is an Application built-in to Mac OS X which allows the user to alter system-wide preferences - i.e. preferences that will affect the whole OS and not just one application.

The application basically allows selection of different preferencePanes (commonly called prefpanes)

It is a Cocoa application and these panes are bundles that are loaded dynamically.

This means you can write your own prefpanes by subclassing General/NSPreferencePane

See also General/NSUserDefaults and General/CFPreferences.

----

General/NSUserDefaults seems to allow only storing data under the com.apple.systempreferences domain, not under your own domain. For that, you must use General/CFPreferences, either directly or by subclassing General/NSUserDefaults.

You may also use apple private methods of General/NSUserDefaults�:

    
@interface General/NSUserDefaults (General/ApplePrivate)
- (id) objectForKey:(General/NSString*)key inDomain:(General/NSString*)domain;
- (void) removeObjectForKey:(General/NSString*)key inDomain:(General/NSString*)domain;
- (void) setObject:(id)object forKey:(General/NSString*)key inDomain:(General/NSString*)domain;
@end

