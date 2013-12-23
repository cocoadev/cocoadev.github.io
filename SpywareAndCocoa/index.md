---
layout: page
title: SpywareAndCocoa
---



(Please answer this by adding replies at the end of the page and not inside the text, or the discussion will quickly become unreadable.)0

As a former Windows user and programmer who's moved over to the Mac full-time, I've had lots of fun both using and programming for OS X. However, having used Windows, I still retain a lot of the paranoia one learns using that program, and all its malware and spyware. Looking at OS X through those eyes, I must say I'm feeling uneasy. OS X has so far been spared the attention of spyware writers, but it does seem fairly certain that this is more because of its small market segment than because of any higher level of security.

It is true that the BSD underpinnings of OS X offer quite a solid and secure base system. But these days, Windows is pretty securely locked down too, yet spyware and malware infest it even more than before. Now, with the knowledge I've gained from programming apps for OS X, I can't help but wonder what I'd do if I were a spyware writer. What vectors of attack are there?

First, how do I get code onto a machine? Vulnerabilities have never been a good way to install spyware, as they are unreliable and get patched as they pop up. Much better is to trick the user into downloading a file and running it. However, users may be reluctant to run strange apps (although many of them will do so anyway). However, as it turns out, OS X does not make it very easy to tell if I am about to run an app or not! The .app extension is hidden by default, and an app can use any icon whatsoever. I can create an app named Hotchick,jpg.app and use a standard Preview icon as the app icon, and very few users will notice that they are about to run an application. This is the case on Windows, too, and it is most definitely used as an attack vector there. 

Assuming that I've tricked a user into running my app, what can I do? Here, many will argue that the default Unix security model will protect the system, because the user will not have full access to the system and can not harm it. However, a lot of malware is not really interested in root access. A spam zombie does not need root access to send mail or post spam on blogs. Spyware does not need root access to look at my browser history or contact list. Neither does it need it to report its finding back home.

Now, on Windows, many users are running application firewalls. It is almost a necessity, to protect against malicious software. However, on OS X, such things are nearly non-existent. There is Little Snitch, but costs money and is not in wide use. Many normal programs, including Apple apps, phone home as a matter of course, making it harder to spot when a malicious program does so.

What further mischief could a malicious program cause? It may want to get a close look at the internals of other programs, such as a web browser. It might also want to inject ads into said web browser. Now, once it is running as the current user, it has full write access to ~Library. It can easily add an InputManager here, which will allow it to inject code into every application! This isn't the only attack path, either: Most apps have writable app bundle directories, and it would be easy to create a Frameworks directory inside an app bundle and put in a replacement for a system framework!

Once you've injected code into another application, the possible attacks vectors really multiply. There is no end to the mischief one can create with internal access to the data structures and classes of a Cocoa program. Even more worrying, one could inject code that just lies dormant in every program until one of them asks for root access from the user. The dormant injected code can now awake and run with full access to the entire system! Installers and updaters ask for root access often enough that this would not take long.

So in summary, these are some of the problems I can think of off-hand:
1. Applications are not obvious. It is too hard to tell when you are about to run code and when not.
2. Tools for monitoring suspicious activity by programs do not exists, or are not used. This does not only mean TCP sniffing like Little Snitch, but tools to monitor attempts at code injection are sorely needed.
3. OS X is too trusting. Code injection is trivial. Once you get past the outer defenses, everything is wide open, even root access (Assuming my suggested attack works - I have not tested it.)

It all adds up to a feeling that the OS X community is not prepared to deal with spyware and malware, should the authors of such decide to focus their attention on OS X. So far they have not, and in the future they may not either, but I would prefer not having to rely on this.

So, what are everybody's thoughts on these matters? - WAHa

----

Anyway, to answer:

First, you get a confirmation requester only the first time you run application WITHOUT clicking on it! That is, by clicking on a file that will be opened with that app! The first time you click on app directly, you get NO requester.

Second, installing yourself to run at login does not need administrative access that I can see. Neither do you need to, if you just inject an InputManager instead of loading a task.

Third, you misunderstand the purpose of an application firewall. The built-in firewall stops INCOMING connections. It does nothing for OUTGOING connections. An application firewall is for stopping applications on YOUR machine from connecting OUT, because you do not trust them. Little Snitch is, as I said, the only one I've been able to find for the Mac: http://www.obdev.at/products/littlesnitch/index.html. If you try and run it, you will notice that there are quite a number of apps that DO phone home. iTunes is one, BBEdit is another, Teleport is a third and Flip4Mac a fourth that I have installed. This is just a random sampling.

Fourth, the default user created on an OS X install is part of the admin group, and has write access to most apps in /Applications/, even without giving a password. Further non-admin users probably do not, but I suspect many are like me and only run with that first user created.

In the fifth case, I was not describing a trojan horse, I was a describing a case of privilege escalation. Looking closer, it appears authservices only lets you launch a second process with higher privileges. However, since your code image is infected, it is quite possible to just replace the path to the executable that will be run with full privileges with one of your own, and your app gets root access.

-WAHa

----

Well, Apple *is* doing something. For example, 10.4.3/Intel now requires an executable to be running with the privileges of the procmod group before what I call "direct injection methods" (ie the mach calls used by mach_inject) work. And non-root-owned things are not loaded onto root-privileged apps. We'll have to see where Leopard will bring us. -- EmanueleVulcano aka l0ne

----

*Even more worrying, one could inject code that just lies dormant in every program until one of them asks for root access from the user. The dormant injected code can now awake and run with full access to the entire system!*

I don't think getting root access is this easy. When an application needs to perform a task as root, the user will be asked for a root password, using a dialog. This dialog is not created by the application itself, but instead is created by the Security framework. The application will use a small helper tool (command line application) to perform the authorized task, the helper tool is only executed by the Security framework with root permissions when the user authenticated itself using the dialog succesfuly. See also MoreAuthSample
-- JorisKluivers

[ok misread the reply 2 positions above this one, which actually says what I describe here]

----

Yes, and as I pointed out, if the code calling AuthServices is already infected, the string that holds the path to the executable to run may have been swapped to another one. One way to do this I can think off the top of my head is to replace fileSystemRepresentation in NSString with one that returns the malicious path for certain strings. - WAHa

----

The devil is in the details. Name the mechanism by which AuthServices can be initially infected and your argument will have weight.

----
You don't have to infect AuthServices, just a trusted application which can make a plausible call to AuthServices.

----

RIght, exactly. You just need to alter the arguments sent to AuthServices, which should be easy if you install a rouge InputManager, for instance. - WAHa

----
In theory ... but you still have yet to name the specific mechanism. If it's really *that easy*, OS X is as secure as a little girl's lockable diary.

----

I did name a specific method - overriding NSString. It is theoretical, though, because I did not actually try it. Point is, once your code image is infected, it's *very* hard to trust anything.

Getting root isn't even the point, though, as I explained before. You're stuffed from the moment your code image is infected, even if you don't try and gain root access. That was just an example of something dangerous that can happen as a result. - WAHa

----

Actually, I am overthinking this. It's even easier than that. Just install an InputManager, and then check when you are loaded into Installer.app, and just pop up your OWN AuthServices request! It will look like the installer is asking for access, when in actuality it is the malware. - WAHa

----
Have you done this? That's my point. Try it. See if it really is that easy. I think you'll be surprised to find that it most certainly is not.

----

Ok, which specific part of it is hard? -WAHa

----
Plainly put: You're challenging the OS X security model by suggesting it's trivial to achieve privilege escalation through an input manager, or even by modifying NSString. You've been asked to demonstrate how to do so. All you've done since is skirt the issue. If you wish to have a real discussion about a real security threat, take a stand and produce your proof by spelling out exactly how to achieve this so it can be tested. Anything less makes you a troll.

----
Don't go calling others 'troll' when you, yourself, don't even sign your posts. -Seb

----
As I've said multiple times now, this is only a minor side claim. Even if I couldn't do this, the problem would be just as severe. I have not tried this because it is not important for the discussion at hand. That said, I'll look into making a practical example. But for now, try to focus on the main topic.- WAHa

----
To the "makes you a troll" guy: write an InputManager. In your InputManager primary class's     -init method, make a call to AEWP, pointing to a script inside the IM bundle. Have the script, which will be run as root, thoroughly 0wn the user's computer, then delete the InputManager as it is no longer necessary, and why leave evidence around?

The auth dialog triggered by AEWP will appear to come from the first Cocoa app launched after the InputManager has been installed. Indeed, it **does** come from that app, as your IM code is loaded into the app. If you think the user will be unduly suspicious at an AEWP dialog coming from Mail or iCal, add a very simple check for the bundle ID of     [NSBundle mainBundle] to make sure it's on a list of apps that are likely to make legitimate AEWP calls.

A hyper-vigilant user will check the auth dialog and hopefully notice that something is amiss, but how many people actually do that? I know that I personally just type in my password any time Installer.app asks me to after I've double-clicked a password. If an InputManager like that were installed on my system in such a way as to subvert Installer.app, I'd be gone the next time I tried to install a .pkg.

What part of this process do you think is difficult? I've never put all the parts together, but I've done each thing individually and none of the components are even remotely hard to build. -- MikeAsh

----

All right, as requested, I did this, and it was about as easy as I thought.

http://wakaba.c3.cx/stuff/InstallerInfector.zip

Download and unpack this, copy the InstallerInfector folder into ~/Library/InputManagers, then get an installer package that needs authorization (I tested with the iTunes 7.0.2 installer), and run that. Click forward normally until you get a password requester. Enter your password (if you trust me here, of course). The installer will not continue (I didn't bother figuring out how to make that work).

Look in your home directory. There should be a file named "SystemOwned" there, owned by root. - WAHa

----

Yes, we know these things are possible and not at all difficult. If you have concerns about security or have found exploits please logs bugs at radar.apple.com.
This isn't the best place for a discussion of this nature.

----

Well, it's pretty obvious from some of the comments that some people really do NOT know or even suspect. And I did file some bugs, but it's hard to figure out what kind of bugs to file when everything is working exactly as designed. I am not entirely sure how to fix the problem without breaking many existing apps. Well, making it a lot harder to inject code would probably help, but I'm sure people would complain quite loudly if they couldn't do it at all.

But it IS worth discussing, because there are things third parties can do. One of them being application firewalls, not just for TCP connections but for other OS actions as well. An app that monitors (and if possible, blocks) suspicious changes to the system would be a great thing. - WAHa

----
Regardless, WAHa should really get the attention of Apple on this. It is a method of gaining rogue root access to the system. Although its not an *automated* process - the user still has to authorise the malicious code with a password, the fact is that most people will just willingly give the computer their password! Gaining root access is the worst because it has the highest privileges and status! Viruses and trojans and the like are going to strive for the highest power because then they can get any info from any account and not just the one logged in. With root access they can do anything they like to your Mac potentially damaging it. Furthermore most people tend to use the same password for everything. Email to computer to bank account etc. The hole presented here provides a very trusty request for your password, then coupled with your Address Book details the malicious code can "phone home" through another app that has been cleared for internet activity such as Safari. Your identity can slip through your fingers. Every hole that can be found should be reported, and hopefully Apple can find a solution to fill in the hole. Any hole that can easily gain root access is a major problem. This is not another tiny bug, this is a huge bug if it becomes popular knowledge.

----
The problem I have with the "Mac is a small market" arguement pride.  For the last 6 years, OSX has been displayed as a security os. Mac users are smug about that fact, and security is the battle cry against Windows. Now if I was a malware writer, I would work my ass off to get something that would spread in the wild against the mac community. There are still millions of machine to infect. But there is no malware for the Mac.  I don't think isnt because of market size, but it would take a super coder to do it.

----

I did file a bug with Apple about the InstallerInfector, but it's too early to say what they'll do about that. I don't see a simple solution that isn't just "disable InputManagers and any code injection vectors forever".

Also, the days of malware writers doing stuff for simple bragging rights are long gone. Malware these days is all business. Botnets fetch a pretty price on the black market, and ad revenues from spyware is also significant. - WAHa

----
Disabling input managers and code injection vectors is silly. I think that the solution to this problem is similar to the dashboard one. The user should be notified and asked for password verification when an input manager is added to the user's or systems Library. Or better yet, only allow input manager addition when the user instigates the addition. Infact what would be even better is a specific application to manage input managers. Installation and Removal of input managers can only be done manually through this application and of course with password authentication.

----
There should be no need to get the attention of Apple on this. Mac OS X has a bunch of officially-sactioned code injection mechanisms (InputManager<nowiki/>s, osaxen, contextual menu items, etc.) and it's blindingly obvious that *any* code injection mechanism can be used to do the things we've discussed. If Apple doesn't know, then they're flying with building-sized blinders on. -- MikeAsh
----
Of course, and it is a brilliant technology. However, the user is usually unaware of when new input manager has been installed or being executed. WAHa's input manager could have slipped into ~/Library/InputManagers without the user noticing and on the next package install the system is owned. Very discreet and smooth. Monitoring the Input Manager folder will give an extra heads up on preventing attacks because the Input Manager it is an obvious and already used vector for virus transport and execution. The input manager mechanism shouldn't be axed, OS X should just notify the user when a new input manager is added. Much like it notifies you when you run a new application for the first time.
----
Yes exactly, this is like the whole Widget ordeal earlier this year. Just notify the user and all the problems resolve (of course any real spyware developer would set the hidden flag and dot prefix the file)... -- DanSaul

----
Warnings don't make the problem go away. Most users will click "OK" to anything that appears. It does save a lot of people from this situation, but it's not a perfect fix.

Also, consider this: most UNIXen, Mac OS X included, have significant local root vulnerabilities. It's much less obvious, but anybody who wants to be naughty and is dedicated enough will be able to get his code running as root without any user interaction at all, bypassing all of these warnings and any other security measures you could take. Hopefully this is too obscure for the bad guys to figure out.

For now, the only 100% reliable way to avoid evil code doing nasty things is never to run it in the first place. Run only applications that you trust, and be careful about what you download. Unfortunately this is much harder than I make it sound. -- MikeAsh

----
"Warnings don't make the problem go away. Most users will click "OK" to anything that appears. It does save a lot of people from this situation, but it's not a perfect fix."

Which is exactly why the default button should be "Deny"/"Cancel" in this case ;) -- OfriWolfus

----
Switching the default makes almost no difference. 99.9% of the activity of this window will be legitimate, and clicking "Deny" will stop whatever legitimate task the user was trying to accomplish. They will quickly learn that you have to click the *other* button, and once that becomes ingrained they'll just click it automatically. -- MikeAsh

----
I think you should read what he said a little bit closer.

----
Can you be more specific? He suggests making "Deny" the default button, and I think that will just make everybody click the non-default button without thinking. What did I miss? -- MikeAsh

----
Actually, turns out *I* misread it. Oops!
----
Perhaps we can make rehab programs for virus writers. Lets get those boring no life people off the streets and into meaningful worthwhile lives. That would solve the whole problem. Its such a pity that some people have to spoil the trust. You should be ashamed of yourselves.
---- 

In case anyone was curious about it, the bug I filed with Apple about the InstallerInfector was, after some strange adventures, closed as a duplicate. Well, I'd expect them to know about it, because as was pointed out several time already, it's blindingly obvious. -- WAHa

----

What adventures were they? I'm dying to know!

----

You're all missing the point.
To have the InputManager in ~/Library/InputManager, code must already have run on the machine (to put the IM there). Apple's philosophy, until now, has been not to prevent anything to run, but to prevent code from entering the machine unattended; this is the underlying theme in their LaunchServices fixes or Installer warnings, for example. You cannot run an application *unwillingly*, but once an application runs it is unrestricted in what it does. -- EmanueleVulcano aka l0ne

----

I'm not sure you should be so quick to accuse others of missing the point. First, I already explained at least one way to unwillingly run an application, by disguising it as a safe file. Second, "once an application runs it is unrestricted in what it does" is nowhere near true - if that was the case, why aren't all users root users by default? Root access is protected for a reason, but as I showed, this is trivial to circumvent. And finally, even if this were "Apple's philosophy", that does not mean it is a good idea! Windows took the same philosophy for a long time, and look where it got them.

(Oh, and as for the adventures, this mainly involved a very confusing request for me to re-classify the bug from a security problem to an "Enhancement". I tried asking for an explanation for this, but I don't think I understood any of the answers I got. Additionally, I didn't even have the option to change the classification in the first place, making it all the more confusing. Finally, somebody else changed it to an "Enhancement", and then some time later closed it as a duplicate.) -- WAHa

----

It's January 2007 and I have some sort of browser hijack happening and no info anywhere on how to solve it.  BOTH Safari & Firefox. I have posted about it at the Little Snitch forum http://forums.obdev.at/viewtopic.php?t=193 

If anyone can help me can you please post at that site because we are at a loss and verging on panic since Macs are not supposed to do this kind of sh*t according to all the marketing materials and hype. Obviously, I am not a developer or programmer or any kind of expert but I desperately need some help. Please?

----

Which "marketing materials" lead you to think you can't hijack a browser on OSX?

----
*"... but I desperately need some help. Please?"*

This is a developer community for developing Cocoa apps, not a support forum for Mac security technical issues. You'd be better off keeping an eye on sites like apple.com/support and macfixit.com.

----

InputManager and mach_inject make a nice combo, even with the procmod requirements. You could fairly easily bootstrap from: 1) Some closed-source code -- freeware/shareware or even trickier as WAHA described 2) insert an InputManager 2) Watch for NSTask launching code that is setuid, and pop up an authorization for "Repairing" the code -- would be fairly unsurprising 3) Run your own code AEWP 4) Create a procmod executable hidden somewhere (in plain sight is good enough -- say replace dynamic_pager with your own dynamic_pager) 5) Erase everything 6) mach_inject 7) Profit.

Alright, it's more than three steps. But it could be done fairly consistently. It would be permanent, and the only permanent visible evidence would be a checksum on dynamic_pager and its procmod sgid bit. And on the ppc you don't even need the sgid bit.

For the earlier poster who said that this is not the appropriate venue -- do you imagine that this hasn't been thought out before? Just do a google. Or that this hasn't been fed to Apple for years? The point is to make folks aware -- caveat emptor might help produce market pressure towards security. I try to avoid installing anything that I haven't compiled from source.

----

"do you imagine that this hasn't been thought out before?"

When I stated that this site wasn't an appropriate place for this discussion I also stated that "we know these things are possible and not at all difficult."

"Or that this hasn't been fed to Apple for years?"

Yes, we're ALL aware of this.

"The point is to make folks aware -- caveat emptor might help produce market pressure towards security"

You will not find many "folks" around here. It's a site for people writing Cocoa apps. Not a site for people writing spyware or regular endusers (i.e folks).

----

Umm, developers are exactly the kind of "users" that can apply more market pressure --- at the end of the day, they're the real customers of the OS producers, not the "end users." They're the one who understand the issues.

Second, the specific mechanisms are particularly important for folks in software. They have the most to lose if their systems get damaged, or have their T3's overwhelmed with noise. And being aware of the specific modes are needed to defend against them, not a general arm-waving "we know these things aren't difficult." Some developers don't know how easy, specifically, and what are the particular dangers.  I've been developing for a while on osx, and until I started playing with cocoa and looking at some of the underpinnings of the system, was blissfully unaware of such things as swizzling, and the fact that it's quite easy to add and over-write (read-only, executable) pages to existing processes as they run, via mach calls.  If you're lifeline is working on these machines, as opposed to sending grandma emails, an awareness of these issues is important.  Approaches like checksumming your executables regularly becomes much more important, deep into your network. Or how much you isolate your servers from your terminals.

Most regular end users have little capability or, honestly, need to defend themselves. Spyware writers have a great desire to keep these issues in the dark, as does the OS vendor. It's the middle layer of developers and small software houses that have the greatest need to publicize and understand these issues, to protect themselves and their businesses, whether they're a five man shop or a middle manager of a bank IT department.

