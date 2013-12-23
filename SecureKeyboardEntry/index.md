---
layout: page
title: SecureKeyboardEntry
---

Secure Keyboard Entry (named here for the menu item in Terminal.app that turns it on and off) is a special mode of input that promises to thwart keystroke-loggers. It can also cause problems; certain input methods (like the US Extended keyboard layout) and applications (like the game Escape Velocity: Nova) don't work properly (if at all) when Secure Keyboard Entry is on.

Perhaps the most common use of Secure Keyboard Entry is by NSSecureTextField, which enables it whenever it becomes the first responder.

Secure Keyboard Entry can be controlled programmatically using Carbon Events, which calls it 'Secure Event Input'.

    
extern OSStatus EnableSecureEventInput(void) AVAILABLE_MAC_OS_X_VERSION_10_3_AND_LATER;
extern OSStatus DisableSecureEventInput(void) AVAILABLE_MAC_OS_X_VERSION_10_3_AND_LATER;
extern Boolean IsSecureEventInputEnabled(void) AVAILABLE_MAC_OS_X_VERSION_10_3_AND_LATER;

*--HIToolbox/CarbonEventsCore.h*

