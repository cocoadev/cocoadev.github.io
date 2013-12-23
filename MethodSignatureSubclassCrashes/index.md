---
layout: page
title: MethodSignatureSubclassCrashes
---

Hello again. Once again tackling the TrampolineObject problem of implementing -select, I realized that of course I really ought to subclass NSMethodSignature and override -methodReturnType and -methodReturnLength to return @encode(id) and sizeof(id) respectively.

So far so hoopy, except that it SIGBUSes in Apple's code now; I wonder if perhaps the runtime is making use of e.g. the @defs() mechanism.

Either way, does anyone know if this was to be expected? Is there any way I can get the behaviour I want (method signature identical to normal except for always having a return type of id) without resorting to UndocumentedGoodness? This problem is getting more and more annoying daily.

Thanks. -- RobRix

Oh, just so I don't forget; could somebody perhaps post this to Apple's cocoa-dev list? I'm not subscribed because I don't find it generally useful enough; the signal-to-noise ratio isn't good enough for me. Thanks. -- RobRix

You could always subscribe for the duration of getting a response. I'm a little wary of posting a question I don't understand :) -- KritTer

I could, but that would require me to be less lazy. Oh well, I will. -- RobRix

Posted. No answer yet. *tries to be patient* -- RobRix

Still no answer, so I once again unsubscribed--not that it matters, since LSTrampoline is up and running! This discussion has now been retired. -- RobRix

I see your retired and raise it an orphaned :) -- KritTer

I see your orphaned and keep it in the RecentChanges just for the heck of it. -- RobRix

