---
layout: page
title: GPLQuestion
---

Are you allowed to use GPL code in a program that you sell? Couldn't you just distribute the GPL'd code with your program? Or does your entire program have to be open source if you include GPL'd code?

----

If your program constitutes a "derived work" of the GPLd code, then you must license the entire program under the GPL. Exactly what constitutes a "derived work" is a bit fuzzy, but it's generally considered that including any GPLd code in your program or linking against a GPLd library is sufficient to force you to GPL your code.

Note that there is nothing in the GPL that prohibits you from selling your program. The GPL doesn't prohibit commercial use, it prohibits restrictions on redistribution. You can sell a GPLd app, but you have to provide the source to all of your customers and they have to be allowed to redistribute the program at will, which can make it hard to maintain sales.

----

I'm not positive on this, but I don't think the GPL mandates that you distribute the code together with the built product. You just have to make the code available (as a separate download, or through email). Also (and again, I could be wrong), GPL or no, the original author of the code always holds the rights to it. If your use of the code is minimal, you can email and ask for an exemption in return for credit and a free license to your program.

----

You're right on both counts. You can distribute binaries without source under the GPL, but you *must* provide the source to the people you distributed the binaries to if they ask for it. Since the GPL only covers redistribution, the original author is always free to do whatever he wants with the code, including selling a closed commercial version.

----
The copyright holder(s) have the right to license work as they see fit, including licensing it to everyone via GPL and still selling it to some commercial company.  One problem is that if someone else contributes to your GPL file, then they have a share in the copyright, so you no longer can go relicensing it at will.  One possibility is that you ask someone doing a bug fix to release the fix to the public domain.

----
*The GPL generally does not allow linking GPL to non-free; there is an exception for standard libraries, and authors can grant additional exceptions.*
----
You can just ask the contributors to reassign copyright to the author, or to the foundation that owns the copyright on the project. I believe this is what's done with GNU. Releasing a contribution into the public domain is actually not an option. It's a derived work of a GPLd work, and so it must be released under the GPL if it's redistributed.
----

Assigning copyright isn't so simple to do.  For example, to contribute to GNUStep you have to ask for a copyright assignment form, fill it out, sign it and mail it to the FSF.  

As far as releasing changes to public domain, the FSF encourages this for small changes.  "Public domain" has to do with disclaiming copyright, not with licensing.  

http://www.gnu.org/licenses/gpl-faq.html#AssignCopyright

----

*As a quick note, the reason they changed the "L" from "Library" to "Lesser" is because it really doesn't have anything to do with libraries at all, and RMS wanted to avoid the idea that libraries were somehow distinct from other code, and wouldn't use the strict GPL.  You need to remember that RMS considers the GPL to be a **social** contract, and not a business one.  The idea of Free Software is, for him in particular, a moral stance.*

CocoaTech, creators of the Path Finder file browser, is learning this lesson the hard way.

*Well, it's all in the FSF faq, so silly them for not reading it.*

What problems are CocoaTech having?

**Google is Your Friend:** *In the latest version of their Finder-replacement product, PathFinder CocoaTech has included a terminal feature based on sourcecode from the GPL'd iTerm. They have made available the classes they developed from the iTerm sources, but not all the sourcecode for their product.* Well, it was a a hair more complicated.  They didn't think they were doing anything wrong because they GPL'd the framework that used the code.  The GPL is a little bit ambiguous here.  

*The GPL isn't at all ambiguous. If you use GPL'd code, your code is GPL'd. If you wrap GPL'd code in a library, your library is GPL'd. If you link against a GPL'd library, your application is GPL'd. The LGPL is a bit ambiguous, so it's going away.
*

Is too ambiguous! ;-)  There's nothing in the text of the GPL about dynamic linking.  Check out the text from the faq (and read the original license):

    
If the program dynamically links plug-ins, and they make function calls
to each other and share data structures, **we believe** they form a single
program, which must be treated as an extension of both the main program
and the plug-ins.


Did you catch that?  This is how they want it interpreted, that doesn't make it fact.  

*
Section 2B of the license. Section 5. There's no ambiguity about linking simply because linking isn't mentioned. It's lumped in with all other uses because it is not considered any different.

The LGPL is where the ambiguity lies, since you are allowed to link. Is Java's import statement linking or using? Some say linking, the FSF says using. Apache won't touch LGPL'd code.

Not understanding the GPL is a poor excuse. If you don't intend to release your code under the GPL, avoid GPL'd or LGPL'd code.
*

Section 2B:
    
    b) You must cause any work that you distribute or publish, that in
    whole or in part contains or is derived from the Program or any
    part thereof, to be licensed as a whole at no charge to all third
    parties under the terms of this License.


Section 5:
    
  5. You are not required to accept this License, since you have not
signed it.  However, nothing else grants you permission to modify or
distribute the Program or its derivative works.  These actions are
prohibited by law if you do not accept this License.  Therefore, by
modifying or distributing the Program (or any work based on the
Program), you indicate your acceptance of this License to do so, and
all its terms and conditions for copying, distributing or modifying
the Program or works based on it.


The issue is that dynamically linked code is only arguably part of the program.  This is especially the case because the GPL only addresses copying and distribution.  If a user takes a non-GPL program that accepts plugins, and links it to a GPL plugin that is suitable for use with a number of programs, including some that are GPL, then who broke the agreement?

----

Example of the difficulties:  Suppose I release a plugin for Mail.app, and I GPL it.  Is it legal?  After all, Mail will dynamically link to this plugin, so does this mean that Mail would have to be GPL'd?  Answer: Legal.  The GPL only covers copying and distribution, the user can do whatever they want as far as running.  Okay, so how about if Apple *includes* my GPL'd plugin.  Now it's illegal according to FSF (but it's not totally clear-cut).  *Okay*, so how about if they just give me Mail and the plugin, and let me put it in the right place if I want?  Probably legal.  If they have an installation script that puts it in the right place?  Probably illegal.  Argh!

Later:  Hmm, I was wrong.  See this FAQ entry: http://www.gnu.org/licenses/gpl-faq.html#GPLPluginsInNF .  

----

Note also the legality of public domain software is...murky. Many countries do not allow you to disclaim your copyright. This makes putting your software into the public domain simply impossible. Since no software could have been written long enough ago to enter public domain legally yet (it would have to predate Mickey Mouse), this means public domain software is currently a fiction.

*Well, it isn't a fiction in the US, is it the case that US companies have to treat the source as the author intends? I'm not nearly as worried about being sued by foreign companies..* 

----

See also DWKitAndTheGPL for info on how Acquisition is in direct violation of the GPL (and its author knows this and doesn't care). The only reason he gets away with it is that LimeWire never takes anything to court (and that he has lived, variously, in Canada and Australia).

*so, in other words, the GPL is useless.*

Well, if you want to put it that way... perhaps. Though the GPL is legally binding, it's still pretty much the honor system. This is the exact reason why I despise Dave Watanabe so much.

----

If you're hoping to sell your software, you might want to avoid the GPL and LGPL. The GPL is, after all, all about making software free. That includes the software you want to sell. If you want to sell your software and avoid moral/legal implications, the GPL is not your friend. 

----

The GPL has been enforced several times in Germany, and has been used to compel people to release their source code several times in various places. It must, however, be noted that it's a retarded license. If you want to release your source code, please consider the BSD License or the Creative Commons licenses.

----

I tried to get this answered through looking at resources on the web, as well as asking on Google Answers (http://answers.google.com/answers/threadview?id=439136), and basically the conclusion I came at is: don't even bother thinking about using code which has a licence with the letters 'G', 'P' and 'L' in its title if you want to release commercial software (non-proprietary or not).

Not because of the spirit of the licence, but because noone, not even its author, can provide a clear answer to simple questions. When the author answers these questions with "It depends", clearly it's not something that's worth your time to bother with; yes this means you'll want to avoid code that saves you 40% of your work, but trust me at least you'll know how the code you DO use is licenced and how you can use it.

Besides, I have yet to find a library I needed that didn't have a better equivalent in non-*GPL version (libxml for example; much better and faster than others; MIT licensed).

Pitty too, since I find GPL stuff that implements protocols and standards only seem to hinder the acceptance and promotion of those, especially on less-popular platforms.

----

The GPL is expressly designed to destroy 'proprietary' software. Unless you buy into the FSF's 'all software is free' utopia, it's best avoided.

No this is better:

The GPL is expressly designed to allow programmers to openly share each others software source code for free, so long as that source code and future source code based on previous free source code stays free. So that free software developers can benefit from fellow free software developers. Pretty simple really.

----

No so simple when what you want to use is a utility library that implements, say, a protocol or other popular 'new' technology. Authors usually want to release it as open so it's adopted by others, but just blindly license it under *GPL. It just becomes a total mess at that point since noone can (or wants) to answer simple questions as to its proper use.

----

I have to say, after dealing with GPL issues for so long, that the Creative Commons licenses are much better, if only because they make things simple. The main problem with the GPL is its complexity, especially in explaining it to people who don't know what it is. For example, see http://creativecommons.org/licenses/by-sa/2.0/ for a well-written, easy to understand alternative to the GPL. --RobinHP

----

An alternative that I like is the MIT license:  http://www.opensource.org/licenses/mit-license.php

I found that it is one of the most concise of the open licenses and it is relatively non-restrictive.

----

**New Question** :)

OK so what if you have some GPL command line program and you simply use NSTask to call it in your program and you distribute it in your program, yet your program is not OSS. Is this legal? If so, let's say you had only a class that was GPL'd... could you compile this into a command line app and open source that but also call that via NSTask or an equivalent?

*IANAL, but yes on both counts.*

I disagree. I am not a lawyer either (I personally find the acronym IANAL to be vaguely disturbing), but I think that both would be fine only if the pieces were distributed separately. When distributed together, they would violate the GPL. See the all-powerful section 2b of the license. I think that when including an executable with your application's bundle (a la Acquisition), your app quite clearly "contains or is derived from the Program or any part thereof" and thus should be licensed under the GPL. Frankly, this seems pretty clear to me. Even though you are putting the programs in two different processes, they still contain *parts of the original program* and thus should be released under the GPL. --RobinHP

----

From a different perspective, as an initial author of a GPL'd software project, I primarily enjoy writing software as it is fun being creative. I want to share it freely with others, but at the same time, do not want others (companies) to make a direct monetary profit from my work or larger works, hence the larger works source code must also free. If I share my work under the GPL, my work will continue to be open and free to others.

----

"I want to share it freely with others, but at the same time, do not want others (companies) to make a direct monetary profit from my work or larger works."

This seems to be the case for a few people I know.  I wish I could understand let alone empathize with this point of view.

----

The creative commons licenses are also worth a look: http://creativecommons.org/about/licenses/meet-the-licenses

----

Interestingly the page you refer to ends like this: **"and finally, for those licensing software, we offer the GNU GPL and GNU LGPL licenses."**

----

It sure does, but don't get the wrong idea - the creativecommons licenses can be used to license ANY creative work (with attribution for the original creator). Source code included.

