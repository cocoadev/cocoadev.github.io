---
layout: page
title: CoreGraphics
---



CoreGraphics (AKA Quartz) is the framework representation of the window server. The framework is part of the ApplicationServices umbrella framework.

Quartz is Mac OS X's powerful imaging model, based on Display PDF, and does everything from handling the management of windows and low-level events through to rendering PDFs and drawing bezier curves.

**Quartz Compositor** is a system process which manages the state of the windows on screen, running applications and interface events, and compositing all the layers together into the lovely sploogy Aqua goodness we all love. It has an API, see Quartz Services below.

**Quartz 2D** is the PDF-based vector drawing system which you can use from both Cocoa and Carbon. You can also use it for importing and exporting PDF content. In Cocoa, you'll probably use it via NSView and NSGraphicsContext, but you can drop down and all the C APIs if you want (use -[NSGraphicsContext graphicsPort] to obtain a CGContextRef). See for documentation on the Quartz 2D API.

**Quartz Services** is the low-level window server API. There are public APIs for the configuration and control of display hardware (including performing gamma fades, capturing the display for exclusive use, and switching modes), useful for full-screen applications such as games, and remote operation of the desktop (manipulate low-level events, register a screen refresh callback, watch for windowserver death), useful for applications such as Apple Remote Desktop, VNC and Timbuktu.

Sample code for Quartz 2D and Quartz Services is available.

Links: 
* [http://www.goldenfibreglass.com/product-tangki-fiberglass.php Tangki Fiberglass]
* [http://www.goldenfibreglass.com/product-atap-fiberglass.php Atap Fiberglass]
* [http://saranasukses.com/outsourcing-indonesia.html Outsourcing Indonesia]
* [http://www.doxapest.co.id/index.php/pest-control-dan-anti-rayap Pest Control]
* [http://littletods.com/en/content/4-perlengkapan-bayi Perlengkapan Bayi]
"Quartz Composer" 

Some non-apple-documented functions are documented here : CoreGraphicsPrivate.

