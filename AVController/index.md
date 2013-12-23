---
layout: page
title: AVController
---

    - (id)init;

    - (void)setDelegate:(id)delegate;

    - (void)setCurrentItem:(AVItem*)item;

    - (void)setCurrentItem:(AVItem*)item preservingRate:(BOOL)flag;

    - (void)setCurrentTime:(double)time; // NSTimeInterval?

    - (void)play:(NSError*')outError;

    - (BOOL)isCurrentItemReady;

    - (void)pause;

    - (void)setRate:(float)rate error:(NSError*')outError; // 1.0 = normal?

    - (float)volume;

    - (void)setVolume:(float)volume;

**AVController delegate methods**

    - (void)queueItemWasAdded:(id)foo;


Category:PointlessInformation

