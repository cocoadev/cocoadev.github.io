---
layout: page
title: CreatingiTunesVisualPlugins
---

Ok, this isn't strictly Cocoa related, but I can't find any better place to ask it.  Does anyone know how I would go about making an iTunes visual plug-in?  The Apple iTunes Visual Plug-ins SDK ( http://developer.apple.com/sdk/index.html ) is where you'd think to go for the neccessary files, but the sample plug-in they have doesn't work, even though it builds without errors.  Can anyone point me in the right direction?

-- BrianMoore

File a bug report? -- RobRix

----

On Jaguar you need a file named PkgInfo in the Contents directory of the bundle to tell OS X that the bundle is in fact a bundle (and what type it is, despite having already told it this in the Info.plist). For iTunes plugins the PkgInfo file should be the eight characters hvplhook. -- SamTaylor

Wow... putting that file there made it work. Cool. I'm gonna file a bug report about this, definitely. Thanks. -- BrianMoore

----

I think Apple's line is that 'it's a feature, not a bug.' Maybe if enough people file bug reports... -- SamTaylor

