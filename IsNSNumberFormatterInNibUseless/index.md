---
layout: page
title: IsNSNumberFormatterInNibUseless
---

In my NIB file, I can drag an NSNumberFormatter on a text field or table column to format its content. However, the formats proposed are all US-centric. I clearly don't want to hard code number formats in my NIB or in my code. How can I make the NSNumberFormatter format numbers using the system defaults? The localize check box doesn't seem to do anything.

� OlivierScherler
----
Because you can localize nib files, it is fine to hard code number formats in them. When you make another localization of your nib, simply put in another format. --JediKnil

That's far from optimal -- language, date and number formats are all different settings and the settings should ofcourse be used as far as possible. Also, I enjoy having my native date/number format even in (for example) english-only apps. --EnglaBenny

Indeed, locale setting is separate from language setting. (There are numerous currencies that might be used in an English-language app, for instance.)
----
Yes, Olivier, you are right, IB is useless in this regard.  Please file a bug against it. The only thing to do is add the NSNumberFormatter with IB, then in your code fetch the correct format string from the defaults database and pass it to the formatter. --smcbride

