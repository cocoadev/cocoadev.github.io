---
layout: page
title: UnknownComponentManagerBug
---

 I have an error that I have no idea where it came from or what's causing it, except a small hunch it might  be from General/QuickTime.Ideally, the user hits a button in a window, and a drawer drops down beneath the window containing an General/NSMovieView that plays an AIFF, WAV, or SND. However, when I do this, I almost always get the following message:    ## Component Manager: attempting to find symbols in a component alias of type (regR/carP/x!bt)The drawer still drops down fine though, and the sound file plays. It seems to work fine, but I'd like to know what the message means.----See if you have the Toast VCD component in /Library/General/QuickTime and remove it (assuming you don't want/intend to use it).
