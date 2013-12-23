---
layout: page
title: NSWindowLevel
---


*     NSNormalWindowLevel - The default level for NSWindow objects.
*     NSFloatingWindowLevel - Useful for floating palettes.
*     NSSubmenuWindowLevel - Reserved for submenus. Synonymous with NSTornOffMenuWindowLevel, which is preferred.
*     NSTornOffMenuWindowLevel - The level for a torn-off menu. Synonymous with NSSubmenuWindowLevel.
*     NSModalPanelWindowLevel - The level for a modal panel.
*     NSMainMenuWindowLevel - Reserved for the application�s main menu.
*     NSStatusWindowLevel - The level for a status window.
*     NSPopUpMenuWindowLevel - The level for a pop-up menu.
*     NSScreenSaverWindowLevel - The level for a screen saver.
*     kCGDesktopWindowLevel - Under the desktop. (Still accepts mousedown so desktop is useless where the window is located)


Another useful way to specify window level is with     -[NSWindow addChildWindow:ordered:].  This allows you to place a window always directly over or under a related window (and will also cause drags of the parent to drag the child as well).  Think of a sheet.

see also

*WindowAlwaysInFront
*SplashWindow
*GlobalFloatingWindow
*PopOutWindowFromTitleBar

