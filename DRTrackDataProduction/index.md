---
layout: page
title: DRTrackDataProduction
---

Informal protocol that you must implement when you're providing your own data for a DRTrack. (See DiscRecording for an intro to this topic.) The methods in the protocol are:

    
- (BOOL) prepareTrack:(DRTrack*)track
              forBurn:(DRBurn*)burn
              toMedia:(NSDictionary*)mediaInfo;
- (void) cleanupTrackAfterBurn:(DRTrack*)track;
- (uint32_t) produceDataForTrack:(DRTrack*)track
                      intoBuffer:(char*)buffer
                          length:(uint32_t)bufferLength
                       atAddress:(uint64_t)address
                       blockSize:(uint32_t)blockSize
                         ioFlags:(uint32_t*)flags;
- (BOOL) prepareTrackForVerification:(DRTrack*)track;
- (BOOL) verifyDataForTrack:(DRTrack*)track
                   inBuffer:(const char*)buffer
                     length:(uint32_t)bufferLength
                  atAddress:(uint64_t)address
                  blockSize:(uint32_t)blockSize
                    ioFlags:(uint32_t*)flags;
- (BOOL) cleanupTrackAfterVerification:(DRTrack*)track;


The most important methods you must implement are these three. The others just provide hooks for doing other work you might want to do.
    
prepareTrack:forBurn:toMedia:
produceDataForTrack:intoBuffer:length:atAddress:blockSize:ioFlags:
cleanupTrackAfterBurn:


When the burn starts, you'll get a prepareTrack call, followed by a lot of produceData calls, followed by a cleanupTrack call. Watch out! The produceData calls usually loop through the track -twice-, once to write and once to verify.

You'll receive the produceData calls on a high-priority thread (NOT the main thread) and you'll need to provide the requested data as fast as possible. Your producer is buffered by a RAM cache inside DiscRecording, so you don't need to have your own RAM cache; you should simply do whatever you need to do to generate the data.

It's ok to block the thread you're called on, for example grabbing a mutex or dispatching work to another thread, as long as you can maintain the sustained data rate needed for the burn in the long run.

