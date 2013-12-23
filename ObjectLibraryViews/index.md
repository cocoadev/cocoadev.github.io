---
layout: page
title: ObjectLibraryViews
---



This page is for tracking re-usable Cocoa view and control classes. It is a subpage of ObjectLibrary.

**RECENT CHANGELOG**


* December 23 2011 - Added JLNFadingScrollView
* August 3 2011 - Updated SRScopeBar
* 15 June 2010 - Added Recessed Source List
* 15 June 2010 - Added NonSelectingTokenField
* 29 March 2010 - Added IGEscapePassingDatePicker
* 11 March 2010 - Added IGIsolatedCookieWebView
* 10 March 2010 - Added IGResizableComboBox
* 4 February 2010 - Added CSIconView
* 30 October 2009 - Added JLNDragEffectManager
* 16 October 2009 - Added JLNTotalBarTextCell
* 2 June 2009 - Removed code from Blackhole Media - the website has been taken down
* 3 December 2008 - Added NAURLTextField
* 19 October 2008 - Added BGHUDAppKit
* 20 August 2008 - Added UKDiffView, slight adjustment to URLs of other UKxxx classes.
* 2 July 2008 - Added views from http://www.33software.com
* 24 December 2007 - Added Shortcutrecorder
* 25 April 2007 - Added GCWindowMenu
* 21 April 2007 - Added Gradientpanel.framework
* 6 January 2007 - Added SCSliderWithTextField
* 6 January 2007 - Split ObjectLibraryViews from ObjectLibrary.

----


***AMButtonBar** - http://www.harmless.de/cocoa.php - Create a bar of buttons, similar to those in iTunes 6 or in the Finder search pane.

***AMIndeterminateProgressIndicatorCell** - http://www.harmless.de/cocoa.php - Indeterminate progress indicator cell. Will work at any size. Also included is a controller class for table columns with progress indicators.

***AMPreferencePane** - http://www.harmless.de/cocoa.php - This set of classes and protocols is used to build, organise and display preference panes. The preferences window allows the same kind of display and sorting of panes as Apple's System Preferences. Preference panes may be build in to the application or provided as loadable bundles.

***AMRemovableColumnsTableView** - http://www.harmless.de/cocoa.php - Adds a facility to NSTableView that lets you easily hide and show columns.

***AMRollOverButton** - http://www.harmless.de/cocoa.php - A subclass of NSButton. Similar to the buttons in Safari's bookmarks bar.

***AMShapedButton** - http://www.harmless.de/cocoa.php - A subclass of NSButton. Supports irregular shaped buttons.

***AMToolTipTableView** - http://www.harmless.de/cocoa.php - A subclass of NSTableView, that allows you to display different tool tips for each cell.

***Assistant Kit** - http://s.sudre.free.fr/Software/AssistantKit.html - The Assistant Kit is a kit to create a Set-up/Installation Assistant using the same User Interface than the one of the Apple Installer  or Display Calibrator utility. (BSD License)

***BGHUDAppKit** - http://www.binarymethod.com/content/bghudappkit.php - BGHUDAppKit framework and IB plugin give developers access to a whole suite of HUD style controls.  Resolution independent as no images are used in the creation of the controls, only native OS X drawing routines.  Appearance is customizable with use of theme files. (BSD License)

***BLAuthentication** - http://www.frognet.net/~lachman/ Class to make implementation of Apple's Security Framework easier. It provides methods to check if the user is authenticated, authenticate the user, deauthenticate the user, execute commands with privileges, get the process ID of any command, and kill processes. **-- This link seems to be broken. Pity. Anyone know a new one?** This seems to work: http://www.etiemble.com/downloads/b/blauthentication/BLAuthentication.zip

***CalendarControl** - http://code.google.com/p/calendarcontrol - This project contains two controls; CalendarControl emulating the calendar found in Leopard's iCal and the iPhone, and WeekControl which provides an more convenient way to represent days than having a matrix of checkboxes. Both are ResolutionIndependent. New BSD license.

***CCDTextField** - An NSTextField subclass with buttons embedded in it on the left and/or right - similar to NSSearchField, minus the searching bits.

***CSIconView** - http://code.google.com/p/csiconview/ - A fairly complete Finder-like icon view, under an MIT license.

*** IFVerticallyExpandingTextField** - An NSTextField subclass which vertically autosizes itself, its superviews, and the window downward as you type and on window resizing.

***DTCPathView** - http://los.dtcurrie.net/code/ - local filesystem path auto-completion in any NSTextField

***FakeImageView** - True proportional scaling

***GCGradient** - http://apptree.net/gcgradient.htm - simple class for filling any NSBezierPath with a gradient. Handles all the low-level Core Graphics shader stuff so you just add colours, set angles, etc. and call the fillPath: method.

***GCJumpBar** - https://github.com/gcamp/GCJumpBar - NSControl subclass that mimics Xcode's Jump Bar.

***GCWindowMenu** - http://apptree.net/gcwindowmenu.htm - neat class for turning any view into a pop-up menu. Call one method to create a menu object with your nominated view, and another to do the rest - display, track and gracefully fadeout afterwards.

***GCZoomView** - http://apptree.net/gczoomview.htm - simple NSView subclass that implements zoom in, zoom out, zoom to fit, zoom to rect, etc with any content that you might draw in drawRect: drop in instead of NSView where you need zooming.

***GradientPanel.framework** - http://gradientpanel.com - complete UI for editing, creating and sharing gradients. Includes enhanced version of GCGradient which can be used on its own (see link above for more details). GCGradient is faster and has some additional features over CTGradient. The Gradient Panel framework is free and is intended to allow a degree of standardisation in Gradient interfaces across a range of applications. *Very nice work!*

***IGEscapePassingDatePicker** - http://2718.us/cocoa/#IGEscapePassingDatePicker - NSDatePicker subclass that passes the keyDown: event to the next responder if the key is the escape key.  MIT License.

***IGIsolatedCookieWebView** - http://2718.us/cocoa/#IGIsolatedCookieWebView - WebView subclass that uses private cookie storage.  MIT License.

***IGResizableComboBox** - http://2718.us/cocoa/#IGResizableComboBox - NSComboBox subclass with a small bar that can be dragged to resize the popup.  MIT License.

***JLNDragEffectManager** - http://joshua.nozzi.name/source/jlndrageffectmanager/ - Drop-in class for adding drag-and-drop animation effects to your app a la Interface Builder's Library palette (icon morphs into actual object when you drag from the palette). Free, BSD-style license (with attribution).

***JLNFadingScrollView** - http://joshua.nozzi.name/source/jlnfadingscrollview/ - A drop-in NSScrollView subclass for providing a fade affect at the top and bottom of scrolled content. Free, BSD-style license (with attribution).

***JLNTotalBarTextCell** - http://joshua.nozzi.name/source/jlntotalbartextcell/ - NSTextFieldCell subclass for showing "total bars" in tables and text fields. Configurable top/bottom bar, style, and color. Originally intended as an informative demo, so packaged as such. Free, BSD-style license (with attribution).

***JTCalendar** - http://www.bassetsoftware.com/osx/jtcalendarwidget/ - Calendar control with IB palette

***JTTracker** - http://www.jtechsoftworks.com/samples/TrackerSample.sit - JTTracker is a public domain implementation of a UI tracker that allows you to work with graphics using grab handles and movement.

***KFCompletingTextFormatter** - http://homepage.mac.com/kenferry/software.html - Easy way to make an autocompleting text field, ala the "To:" field in the composition window of a mail client.

***KFSplitView** - http://homepage.mac.com/kenferry/software.html - NSSplitView subclass.  Programmatically expand and collapse subviews, get notifications when a subview expands or collapses, save and autosave splitview position.

***KFTypeSelectTableView** - http://homepage.mac.com/kenferry/software.html - NSTableView subclass, adds "type-select" or "type-ahead" feature.  Type a few characters to select a matching row of the table.

***LRFilterBar** - http://www.burgundylogan.com/Code/LRFilterBar/ - LRFilterBar is a bit of Cocoa source code designed to replicate the filter bar that is found in some of the Apple apps, such as iTunes and Mail. Implementation of the filter bar is extremely easy, with built in overflow support. Since the toolbar uses NSRecessedBezel (which is only available in Mac OS 10.4), there is a built in backup system which displays a toolbar for users not using Tiger. 

***LRCalendarView** - http://www.burgundylogan.com/Code/LRCalendarView/ - LRCalendarView is a bit of Cocoa source code designed to create a basic monthly calendar. You can easily add events which contain a date, title and any sort of Cocoa object that is attached to the event. 

***MPSearchQuery and friends** - http://apptree.net/mpsearchquery.htm - simple rule-based searching with user interface uses KVC to interface to any typical app's rule-based searching needs. Largely inspired by iTunes' smart playlists, queries can be archived to create persistent searches (e.g. "smart folders" would be easy to build from this).

***NAURLTextField** - http://code.joeldev.com - A subclass of NSTextField and NSTextFieldCell for creating a hyperlink-esque control.

***NonSelectingTokenField** - http://joshua.nozzi.name/source/nonselectingtokenfield/ - NSTokenField subclass that avoids selecting all tokens on end editing or becoming first responder. Good example for manipulating selected range of any NSTextField subclass.

***Pdf417View** - http://sourceforge.net/projects/pdf417view/ - pdf417View is an IB palette for drawing 2D pdf417 bar codes. It's distributed under the LGPL.

***PSMTabBarControl** - http://www.positivespinmedia.com/dev/PSMTabBarControl.html - A Safari style tab bar implementation available under the BSD source license.  Features Metal and Aqua drawing styles, animated drag/drop and show/hide, many config options, and much more.

***RBSplitView** - http://www.brockerhoff.net/src/rbs.html - A complete recode of a split view for Cocoa. Programmatic collapse/expand, minimum/maximum limits on subviews, two-way thumbs for nested split views, many other goodies. Interface Builder Palette. BSD/Creative Commons license.

***Recessed Source List** - http://joshua.nozzi.name/source/recessed-source-list/ - A set of view subclasses to achieve the effect seen in Dictionary.app

***ResponderNotificationWindow** - http://homepage.mac.com/d.j.v./FileSharing13.html - A sub-class of NSWindow that posts a notification and calls a delegate when the first responder changes.

***RowResizableViews** - http://www.eng.uwaterloo.ca/~ejones/software/osx-tableview.html - Open source (LGPL) subclasses of NSTableView and NSOutlineView which allows tables and outlines to have rows with varying heights.

***Shortcut Recorder** - http://wafflesoftware.net/shortcut/ - control for capturing key combinations. Comes with an Interface Builder palette, is customizable and resolution-independent, and it's already being used in applications. BSD license.

***SRScopeBar** - http://www.flytocode.com/ - A filtering scope bar similar to Mail and the Finder.  Uses groups, data source delegates, Cocoa Bindings, Interface Builder plugin, and more.  Highly customizable for your application. -- Dead link. Now at http://web.me.com/sean.rich/files/srscopebar.html

***SwitchControl** - http://code.google.com/p/switchcontrol - A 'big button' like the one that TimeMachine.prefPane uses and the Server Preferences services panes. ResolutionIndependent. New BSD License.

***SM2DGraphView Libraries** - http://developer.snowmintcs.com/ - a framework for doing 2-D plotting and graphing using Objective-C. Can draw line graphs, scatter graphs (symbols only), and vertical bar graphs; axis labels and a background grid are also supported.

***SourceList** - http://code.google.com/p/sourcelist - An NSView subclass drawing a custom source list view based on the iLife '08 equivalent. This project also contains an 'iTunes Music Library.xml' parser as an example. ResolutionIndependent. New BSD license.

***TextBox** - A text box using a disclosure triangle to switch between title and text box modes.

***TrackingSlider** - http://home.earthlink.net/~zakariya/files/TrackingSlider.zip - a slider with a desired and current setting indicator, for when changes aren't immediate.

***UKDiffView** - http://www.zathras.de/sourcecode.htm#UKDiffView - Class for implementing a 'diff' view not unlike FileMerge shows it. Contains a non-view class that can parse a diff in standard or unified diff formats and apply it to a text file. 

***UKDistributedView** - http://zathras.de/sourcecode.htm#UKDistributedView - NSTableView-like ObjC class that implements a "Finder Icon View" with repositionable items, grid snapping, selection rectangle, selection etc.

***UKFilePathView** - http://zathras.de/sourcecode.htm#UKFilePathView - NSTextView-like class that displays a file path as a collection of icons and lets you switch between display names and real POSIX filenames, easily hook up buttons to display an NSOpenPanel/NSSavePanel and reveal an item in Finder.

***UKPrefsPanel** - http://www.zathras.de/sourcecode.htm#UKPrefsPanel - A simple delegate class for implementing a Safari-style "Preferences" window. Takes a tabless NSTabView and adds a toolbar to the containing window with an icon for each tab, allowing to switch between them.

***UKProgressPanel** - http://www.zathras.de/sourcecode.htm#UKProgressPanel - Two small classes that implement a window with a list of progress bars like the Finder uses for copying files. Just create a new task object and use it just like a progress bar (it also has a title, status message and a stop button). The window will automatically be created or shown for you, and the task will be added to the list without further work. Written to work in MultiThreaded apps.

***UKSyntaxColoredTextDocument** - http://www.zathras.de/sourcecode.htm#UKSyntaxColoredTextDocument - An NSDocument subclass that implements a text editor with syntax coloring. Pre-configured to do ObjC source code, but can easily be reconfigured using a .plist file to work with many other similar languages. Also supports a "Go to line/character" menu item, and works just fine with documents of a couple thousand lines due to local re-colorization instead of redoing the whole document each time.

***WBCalendarControl** - http://s.sudre.free.fr/Software/DevPotPourri.html - Clone of the control used by the Date & Time Preference Pane to set/display the current day.

***WBSearchTextField** - http://s.sudre.free.fr/Software/DevPotPourri.html - Clone of the control used by Mail.app to search Mails.

***WBTimeControl** - http://s.sudre.free.fr/Software/DevPotPourri.html - Clone of the control used by the Date & Time Preference Pane to set/display the current time.

***WBIPv4Control** - http://s.sudre.free.fr/Software/DevPotPourri.html -  a control which allows to display and enter an IPv4 formatted IP address.

***PasswordPanel** - http://toxicsoftware.com/blog/index.php/weblog/passwordpanel_cocoa_source_code/ - This sample code can (hopefully) be used by any application presenting a change password dialog to the user. Different dialogs allow the user to enter a new password or change a previously entered password. Dialogs can be presented as either standalone modal windows or sheets. Furthermore the user is informed of any problems or weaknesses with the password via a text field. The algorithm used to compute password strength can be overriden. The algorithm supplied with this sample code uses a dictionary lookup to check the password strength.

***FullScreenWindow** - http://toxicsoftware.com/blog/index.php/weblog/fullscreenwindow/ - FullScreenWindow provides a Cocoa NSWindow sub-class that when "ordered front" will span the entire area of the main screen. You can change the preferred screen size and whether to hide the system UI (dock and menu bar). The window can also be used in a normal windowed mode - which makes it very simple to make your GUI switch between windowwed and full screen modes.

***SCSliderWithTextField** - http://ahruman.is-a-geek.net/code/#scsliderwithtextfield - SCSliderWithTextField is a subclass of NSSlider. When it is being dragged, a text field shows the value. When dragging stops, the text field is hidden. This is inspired by the sleep time sliders in the Energy Saver control panel. Unlike those, this implementation also shows the text field temporarily if the value is set by user interaction other than dragging, e.g. with full keyboard navigation. Requires Mac OS X 10.3 or later. (Dead Link - 404)

