---
layout: page
title: OSAnimationTimer
---

A handy class from OpenSwordGroup which allows one to time animation easily.  OSATimers are very simple - only two methods are ever invoked.

Creating an OSAnimationTimer:

+(OSAnimationTimer*)timerWithFramerate:(double)theFramerate

OSAnimationTimer*animationTimer = [OSAnimationTimer timerWithFramerate:30];

and

-(void)sleepThreadUntilNextFrameDate

[animationTimer sleepThreadUntilNextFrameDate];

You can find it, along with example animation code, at http://opensword.dyndns.org/OSATObjectProject.dmg.gz .  You might also want to visit OpenSwordGroup's WikiWikiWeb at http://himitsu.dyndns.org/OpenSwordWiki/ .

The original author feels that no one should ever use this object in this state.

So people can learn from it at there own risk.  Useless policing is useless.

----------------------------------------------------------------

Both links don't exist anymore.

