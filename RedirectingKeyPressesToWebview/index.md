---
layout: page
title: RedirectingKeyPressesToWebview
---



Would it require an NSWindow subclass to send keypresses to the Webview, or am I way off? I already have the webview set as the intialFirstResponder. I need to do this because in my application currently the user is required to click in the webview whenever they want to to press a key (Space or downkey for example), which is rather inconvenient.

Thanks!

*Just -makeFirstResponder: the webview whenever such a need arrises such as enter on the address field.*

