---
layout: page
title: ChooseYourOwnPrefix
---

When making a program (or framework), you sometimes run into a need to keep your classes visibly separate from all the other classes. Some ways of doing this are to use no prefix (so have, for instance, AppController, SomeKindOfDocument, et cetera as your classes), or to use the My prefix (as described in MyApp), or to ChooseYourOwnPrefix.

Choosing a prefix is a mix of looking at which frameworks you're including now (e.g. FoundationKit or AppKit; so you probably never want to prefix your class names with NS), and which frameworks you might conceivably include later. It's all a matter of marking out "namespaces", though it's admittedly not the best way of doing it (Java seems to have done it pretty well, but that's not so helpful to the ObjC coders).

You might want your prefix to be specific to your company (Apple reserves NS for its classes, for instance; intruding into that could easily backfire on you), or to your project. It's really up to you. Just make sure you choose carefully, or it could come back to haunt you.

Your prefix really doesn't have to be an acronym. You can pick one at random, if you wish, or even use your name (RobRixWackyObject would be a bit on the ironic side, though). Project names are also good; SuperAppController is very clear in its intent.

WhenAreNamespacesNecessary?

AlternativesToPrefixing. The best alternative seems to be to use a long "formal" class name in the com.whatever style, but to use a #define to shorten it in your code. --DustinVoss (and I moved stuff off this page to that)

----

Here are a few prefixes to avoid, since there are significant amounts of code using them:



* AA - App Aware
* AB - AddressBook framework by Apple (10.2 and later)
* AF - App Factory (The Little App Factory)
* AFX - Audiffex (http://audiffex.com/ )
* AGP - AGProductions?
* AI - AIUtilities? (Adium)
* AJB - Alf Jørgen Bråtane
* AJ - AppJudo? ( http://www.appjudo.com/ )
* AK - AwesomeKit by Pixate (http://pixate.com)
* AL - The MHA ( http://themha.com/ )
* AM - Apple Automator
* AG - AagaMatrix? (http://www.agamatrix.com)
* AO - All Out Software
* AP - Acoustic Pond ( http://acousticpond.com )
* AS - appscape http://appscape.at/
* AT - Apptentive http://www.apptentive.com/
* AW - AdamWright will make good stuff public soon. Nice NSView subclasses and a framework for genetic evolutionary systems.
* AX - Axman6 (http://axman6.homeip.net/, nothing about programming yet, but there will be in the future)[and yes, i know AX is used with accessibility stuff by apple, but from what i can see, only in C, not Obj-C]
* Ark - Arkonnekt, Sound, AudioUnit? API ( http://arksnd.sourceforge.net/ )
* B3S - Busy Being Born Solutions ( http://busy-being-born.com/ )
* BAG - Berufsakademie Glauchau
* BA - BackApps service by Krynnlance (http://www.backapps.com/ )
* BAUM - Jerome Baum (firstname@firstnamelastname.com)
* BB - Bamboo-language project ( http://code.google.com/p/bamboo-language/ )
* BCC - Bouncy Castle Cryptography, The Legion of the Bouncy Castle (http://bouncycastle.org)
* BD - BrianDunagan
* BE - Incredible Bee ( https://github.com/incbee )
* BG - Buggy Software
* BL - BleuLlama? ( http://umlautllama.com ), but soon to be used in LlamaSettings? ( http://code.google.com/p/llamasettings/ )
* BM - Benedikt Meurer ( https://github.com/bmeurer )
* BN - Brandon Newendorp
* BNR - Big Nerd Ranch
* BOA - Bwolf Of Antbear ( http://antbear.org )
* BTH - Tomek Wójcik (http://www.tomekwojcik.pl/).
* BO - tools and frameworks (aka stuff) by Rene Koecher ( http://bitspinn.org/ )
* BP - Black Positive from developer Gustavo Rago ( http://www.blackpositive.com/ )
* BW - BWToolkit? Framework by Brandon Walkin ( http://brandonwalkin.com/bwtoolkit )
* C - Toxic Software http://toxicsoftware.com/ - I'm greedy. I want a single character prefix too!
* CA - Core Animation (Apple's)
* CB - CamelBones ( http://camelbones.sourceforge.net/ )
* CC - CodeSpoon? Co. Ltd.
* CE - CEUIKit? ( Robert Geifman's CE Code Group, coming march 2007 at http://web.mac.com/robertgeifman )
* CF - CoreFoundation (Apple's)
* CG - CoreGraphics, aka Quartz (Apple's)
* CH - CocoaHeads ( http://cocoaheads.org )
* CI - CoreImage, one-half of QuartzCore? (Apple's)
* CK - Christian Kienle
* CL - Chocolate Framework
* CM - Cocoa Machine ( http://www.cocoamachine.com )
* CO - Case Orange ( http://www.caseorange.com )
* CON - Concurrencylabs
* CP - Cocoa Park
* CR - Coding Robots ( http://www.codingrobots.com )
* CS - CoreServices; e.g. CSCopyUser?'Name, CSCopyMachine?'Name (Apple's)
* CT - CoreText?
* CSF - Commercial Software Foundation, for closed-source apps release under the SPL
* CU - Casual Underground ( http://casualunderground.com/ )
* CV - CoreVideo?, one-half of QuartzCore? (Apple's)
* CY - CubeYellow? Media ( http://www.cubeyellow.com )
* CZ - Carter Allen, CZKit?.framework ( http://www.cartera.me/ )
* DB - [http://www.dropbox.com/developers/core/sdk Dropbox SDK], devboys
* DC - DiGiCo?, Digital Audio Consoles ( http://www.digiconsoles.com )
* DCT - Daniel Tull ( http://www.danieltull.co.uk )
* DD - Dave DeLong? ( http://davedelong.com )
* DDO - dogdaze.org
* DE - devmates
* DF - Dreamfreeze Software ( http://www.dreamfreeze.net )
* DG - David Golding ( http://www.davidgolding.com )
* DHD - Douglas Heriot Design ( http://douglasheriot.com/ )
* DK - DrawKit?, previously GCDrawKit.
* DMCS - Data Management & Consultancy Services Inc.
* DMX - intelligent lighting protocol (DMX512) framework and kernel extension, provided by MacFOH?.kext - http://www.macfoh.com
* DN - Darknoon - ( http://www.darknoon.com )
* DOG - innodog
* DP - Ofri Wolfus ( http://www.dpompa.com )
* DR - DiscRecording (Apple's)
* DRK - Dr.Kameleon and InSili.co (http://www.insili.co.uk)
* DS - Ampede ( http://www.amped.com )
* DS - Dejal Systems, LLC shared code ( http://www.dejal.com/developer/ )
* DSM - DS Media Labs, Inc. ( http://www.dsmedialabs.com )
* DT - DuctTape? (http://culater.net/wiki/moin.cgi/DuctTape)
* DW - David Weiss, Unweary ( http://unweary.com/ )
* DX - Devix Digital Development
* EB - Ekkehard Beier's Cocoa Touch Framework, maybe Open Source soon
* EC - Sam Deane / Elegant Chaos's ECFoundation? Framework ( http://github.com/samdeane/ECFoundation )
* ED - ED frameworks ( http://www.mulle-kybernetik.com/software/EDFrameworks/ )
* EG - Exit Games GmbH ( http://www.exitgames.com )
* EI - Eightt ( http://fountainpen.sourceforge.net/ )
* ELG - Elgato Systems ( http://elgato.com/ )
* EO - Enterprise Objects Framework (Apple's)
* ES - Elgato Systems Turbo 264 framework ( http://www.elgato.com )
* ESS - Eternal Storms Software ( http://www.eternalstorms.at )
* EX2 - Einstein Times Two Development ( http://einsteinx2.com )
* FA - 43 Actions ( http://www.43actions.com ), also the iPhone URL scheme for native apps ( 43actions:// and fortythreeactions:// )
* FC - Fuel Collective ( http://fuelcollective.com/ ) - was Foxcrew, but they seem to be out of business - ( http://www.foxcrew.com )
* FD - Francis Devereux
* FE - fe3c.net
* FES - Frisky Electrocat Studios (http://friskyelectrocat.com)
* FG - Softech di Germinara Francesco ( http://www.germinara.it )
* FL - Framework Labs ( http://www.frameworklabs.de )
* FOH - MacFOH? app suite, plug-ins, frameworks, daemons, drivers, and kernel classes - http://www.macfoh.com
* FR - fabrizioromeo
* FRB - Fernando Barajas
* FS - FScript
* FT - FutureTap? ( http://www.futuretap.com/ )
* FU - Fappulous, iPhone/OS X apps. ( http://fappulo.us ).
* Fx - FxPlug? (Apple's)
* FZA - Fuzzy Aliens Limited (which means it's likely to be security code: http://fuzzyaliens.com)
* GA - GlossyApp?, ( http://www.glossyapp.com )
* GC - GrahamCox, including GCDrawKit
* GF - GroovyFrog? ( http://groovyfrog.com )
* GG - Google software on Mac OS X, like Gmail Notifier
* GLC - Glucode - http://www.glucode.com
* GM - Glenn R. Martin ( http://www.grmartin.me )
* GOF - Go Factory GroupsSDK ( http://www.go-factory.net )
* GN - GNAA Trollforge ( http://www.gnaa.eu )
* GR - Giancarlo Romeo (a.k.a. giancarloromeo)
* GS - GnuStep? custom classes (some of those classes might get ported to Cocoa)
* GT - [http://github.com/libgit2/objective-git Objective Git], various projects by JonathanGrynspan ("gt" from www.ghosttiger.com, his site)
* HA - Honed Apps
* HB - Hog Bay Software
* HC - Hypnocode GmbH? ( http://www.hypnocode.net/ )
* HD - Harpander Ltd
* HF - Harry Flink
* HG - Hans Gerhardt
* HH - Houdah Software / Pierre Bernard - http://www.houdah.com
* HN - Computational Semiotics and Hermeneumatics / Ignacio Cases - http://www.uooh.org
* HX - Hexten
* IA - InfoAccent ( http://www.infoaccent.com/ )
* iB - iBench: The Cocoa Benchmark
* IBL - Inspired By Life (http://trigliceridosaltos.info/)
* IC - IntelliCore? - http://www.intelli-core.com
* ID - Iterative Development ( http://www.iterative.com )
* IH - InterHive? Design and Development - http://www.interhive.net
* IO - socializr.io - iOS framework for social network apps
* IP - DS Media Labs, Inc. ( http://www.dsmedialabs.com )
* IPS - Indie Pennant Software
* IR - IR Labs -(ixopusada research laboratories)- http://labs.ixopusada.com
* IS - Idea Software
* IS - InventiveSoft
* IT - IThinkSoftware
* IV - Inteliv Ltd (http://inteliv.com)
* IX - Iconara also Illuminex? -- MikeTrent
* J - jer
* JA - JohnAllsup?
* JD - Libraries created by Jesse Ditson
* JF - John Fricker Software - ( http://www.johnfricker.com/ )
* JG - Deadbeat Software -- http://deadbeatsoftware.com
* JH - Jeffrey A. Gipson
* JK - Jeff Kelley - http://blog.slaunchaman.com
* JR - JonathanWolfRentzsch?
* JT - JacobBandesStorch -- Also in use by JumpTap, Inc ( http://www.jumptap.com/ )
* JV - Javelin.cc Code (used in Colloquy) --TimothyHatcher
* JvH - Jop van Heesch (GameTogether?)
* JZ - Jumpzero ( http://www.jumpzero.com/ )
* KC - KirkCode? (http://www.kirkcode.com/)
* KF - KenFerry?
* KN - KennettNet? Software Ltd ( http://www.kennettnet.co.uk/code/ )
* KPS - Kent Place Software ( http://www.kentplacesoftware.com/ )
* KS - KediSoft? ( http://www.kedisoft.com/ )
* L0 - EmanueleVulcano aka l0ne (that's a zero, not an O)
* LA - Lord Anubis
* LC - Logica (http://www.logica.com/)
* LD - Lefebvre David
* LG - Louis Gerbarg (http://trigliceridosaltos.info/)
* LH - Lighthead Software
* LI - Lorem & Ipsum
* LIK - Lingon i Korg Software Creations ( http://lingonikorg.com/ )
* LL - Invisible Llama Software
* LP - Lambda Pi Consulting
* LOL - Upcoming Blakout App
* LQ - Geoloqi SDK - https://developers.geoloqi.com
* LR - Logan Design - http://www.burgundylogan.com/Code/
* LS - LaunchServices (Apple's)
* LT - Lauer, Teuber GbR? (http://www.app-developers.de)
* M3 - M Cubed Software
* MAX - Max Mile Srl (http://maxmile.it/) - Upcoming open source libs
* MA - Merlijn Automatisering (http://www.merlijn.nl )
* MAST - mOcean Mobile (http://developer.moceanmobile.com)
* MB - MattBall's initials. No public objects yet, but some are in the works.
* MC - Marcus Fihlon (McPringle?), a Mac and iPhone/iPod software developer ( http://www.mcpringle.ch/ )
* ME - Michael Evan. SunSet Software.
* MF - MonkeyFood, DiggoryLaycock's domain. No public objects as yet - but getting there. :)
* MFR - Madefire (http://madefire.com https://github.com/Madefire/)
* MG - Matt Gemmel
* MGC - Max Goedjen/Clink Apps (http://clinkapps.com)
* MGS - Mugginsoft (http://www.mugginsoft.com)
* MH - Mike Holman (http://www.mikeholman.org)
* MI - less sadist/masochistic than 'My'; also if you're writing apps for bad Tom Cruise movies (really, if someone want this one, change this line)
* MJ - Michael Jedamzik
* MJM - JM Marino ( http://jm.marino.free.fr ) - I would have preferred to use JMM, but it is already used by Apple 'Java MM' ;-)
* ML - DS Media Labs, Inc. ( http://www.dsmedialabs.com )
* MLP - Mainloop LLC (http://www.mainloop.co)
* MK - Mulle Kybernetik (various projects, see http://www.mulle-kybernetik.com )
* MKD - MKD Software (http://www.mkdsoftware.com/)
* MM - Magnet Mobile (http://www.magnetmobile.com)
* MN - MobileNotifier? (https://github.com/peterhajas/mobilenotifier)
* MNT - Mintrus (http://mintrus.com)
* MO - Mike Ferris ( http://www.lorax.com/ ) - MOKit
* MP - Karl Adam( http://www.matrixPointer.com/ ) - Used in all matrixPointer products and code
* MPW - MarcelWeiher ( http://www.metaobject.com/ ) - MPWFoundation, MPWXmlKit?
* MQ - Maquib Software ( http://www.maquib.com/ )
* MR - Mirek Rusin Ltd ( http://www.accommodationdaylesfordvictoria.com/daylesford/ )
* MSWINDOWS - MikeTrent's upcoming operating system. "It'll take the world by storm!" Pre-order yours today.
* MS - Martin Schuerrer ( http://www.schuerrer.org/ )
* MTG - Metagraph, used for Cocoa utilities pertaining to the Metagraph project. (See metagraph.org for somewhat out of date info, and sourceforge someday soon).
* MU - MacUX Project (grmartin.me)
* MV - Used in ChatCore (formally derived from Massinvoa) --TimothyHatcher
* MVS - Molten Visuals ( http://www.moltenvisuals.com )
* Mc - MooreSan's initials, plus it's easier to talk about my McAppController?, McDocument?, McDataSource? etc than MCAppController? etc.
* MW - Monsterworks ( http://www.monsterworks.com/ )
* Mw - SmVtSailor
* MX - mxcantor technologies (still in development)
* My - Example prefix. Use only if you're a masochist. Or a sadist.
* MY - Mooseyard Software, Jens Alfke ( http://jens.mooseyard.com , http://bitbucket.org/snej )
* NA - NetApp? (http://www.netapp.com/)
* NB - Notebook (http://notebook.hortont.com/)
* NC - Neil Cowburn (http://neilcowburn.com/)
* ND - NathanDay
* NDS -
* NF - Ninja Framework (DS iPhone Studio)
* NFX - Nufex Software
* NGS - Nicholas Shanks (http://www.nickshanks.com/)
* NJR - NicholasRiley
* NK - ninja kitten ( http://ninjakitten.us )
* NKT - Nicholas K Tulach
* NN - nudge:nudge ( http://www.nudgenudge.eu )
* NM - newscope Mobile ( http://www.newscope.com )
* NL - NutShell? Framework (http://www.eosgarden.com/ - To be released soon)
* NNS - NińoScript, or "Not NeXT Software" - see below.
* NS - NeXT Software, now known as Apple (or NeXT/Sun.) Not NeXTSTEP - see below.
* NT - nepTune Systems (http://neptunesystems.eu)
* NTS - No Thirst Software ( http://nothirst.com )
* NX - Older NeXTSTEP prefix (starting with version 3, I believe. Earlier NeXTSTEP versions didn't use a prefix.)
* NY - Ninth Yard ( http://ninthyard.com )
* OA - OmniAppKit
* OB - OmniBase
* OBS - ObjectiveSheep?
* OC - OCUnit and OCMock testing frameworks (http://www.sente.ch/software/ocunit/ and http://www.mulle-kybernetik.com/software/OCMock/ )
* OCS - OcianoSoftware - (http://www.ociano.com.br)
* OE - OpenEmu? ( http://openemu.sourceforge.net )
* OF - OmniFoundation, also ObjFW? (even though it's a different framework, it can be used together with Cocoa just fine through the ObjFW?-Bridge package)
* OI - OmniImage? Framework
* OLB - OLB Productions ( http://olbproductions.com ), ( prev: Oskar Lissheim-Boethius, http://oskarlissheimboethius.com/ )
* OM - Orestis Markou ( http://orestis.gr/ )
* ON - OmniNetworking
* OS - OpenSwordGroup?
* OOT - One Orange Tree ( http://www.oneorangetree.com )
* OR - OpenResearch? Software Development OG ( http://www.openresearch.com )
* ORS - Open Reel Software ( http://www.openreelsoftware.com )
* OT - Olive Toast Software ( http://www.olivetoast.com )
* OTR - Off-the-Record Framework
* OTS - Otter Software ( http://www.ottersoftware.com )
* OV - Overcommitted ( http://overcommittedapps.com )
* OW - OmniWeb Framework
* PA - nudge:nudge Punakea ( http://www.nudgenudge.eu/punakea )
* PD - punktDESIGN ( http://www.punktdesign.se )
* PF - Plain Functional - ( http://plainfunctional.com )
* PG - Piston Games ( http://piston-games.com )
* PH - Playhaus (web site forthcoming)
* PlS? (uppercase-P, lowercase-L, uppercase-S) - PlugSuit? classes ( http://infinite-labs.net/plugsuit/ -- some PlS?* classes load in all apps on systems where PlugSuit? is on.)
* PKM - Patrick Kobler Management (http://www.pk-management.com)
* PL - Plausible Labs ( http://www.plausible.coop )
* PM - PeterMaurer
* PP - PabloPrietz? 's initials. No public objects yet, but some are in the works.
* PQ - plasq ( http://plasq.com )
* PS - Pinch Software ( http://www.pinchsoftware.com )
* PS - PubSub (Apple)
* PSC - Promptu Systems Corporation ( http://www.promptu.com )
* PSY - PsychoH?13, Psy| or just Psy, or eventually Remy Demarest ( http://github.com/PsychoH13 )
* PT - ProtoThreads ( http://www.frameworklabs.de/protothreads.html )
* PX - Praxxium ( http://praxxium.com )
* PZ - Pepi Zawodsky ( http://taplemon.com, http://twitter.com/TapLemon )
* QS - QuickSilver (Open Source Launcher & more) ( http://blacktree.com )
* QV - Quellvetica (http://www.quellvetica.com, http://twitter.com/quellvetica)
* QZ - qoerz ( http://www.qoerz.nl )
* RA - R v Amerongen
* RB - RainerBrockerhoff
* RC - RoundCircle? Enterprise Kit
* RD - Romart Development ( http://romart.ua )
* RE - ReNet?.com.au (http://www.renet.com.au)
* RBX - RossDude
* RG -Robert Geifman naturally ( Robert Geifman's CE Code Group, coming march 2007 at http://web.mac.com/robertgeifman )
* RL - Richard Laing ( http://alittledrop.com )
* RMF - Romart Mobile Framework ( http://romart.ua )
* ROK - JepRokApps (http://www.JepRok.com )
* RPC - RPCEmu? ( http://www.riscos.info/index.php/RPCEmu )
* RR - Roman Romanchuk  (http://trigliceridosaltos.info/)
* RRF - Raureif GmbH (http://www.raureif.net)
* RS - Red Sweater Software
* RT - Ryan Townshend ( http://ryantownshend.ca )
* RV - Renewed Vision ( http://www.renewedvision.com )
* RX - Tom Rixé and Rob Rix. Uh oh. Tom, are you my cousin or something?
* SAM - SimonAndreasMenke
* SC - Functions in Apple's SystemConfiguration framework fo detecting network reachability etc.
* SC - Also Spectral Class code frameworks - http://www.spectralclass.com
* SC - Also Scribe Document Management - recentChaos - www.recentchaos.com
* SCG - SwedishCampground?. (Nothing public yet.)
* SD - Sigma D (individual developer, link to come...)
* SE - Software Etudes - http://www.software-etudes.org
* SF - Security Frameworks - http://www.apple.com
* SFC - StoneFree? Crowd - http://www.stonefree-crowd.com
* SH - Sheets - Spreadsheet and Analytics Application - recentChaos - recentchaos.com
* SI - Snap Interactive - http://snap-interactive.com
* SKW - high-performance base/app/core/data frameworks by ShaunWexler, used in MacFOH? suite & plug-ins - http://www.macfoh.com
* SL - Scoreloop - http://www.scoreloop.com
* SL - Stateful Labs - http://stateful.net
* SLP - ShermPendley's initials. Nothing published yet (other than CamelBones), but I have some stuff in the works. - http://shermpendley.com
* SM - Snooty Monkey - http://www.snootymonkey.com/
* SMC - SmartCoon? - http://www.smartcoon.com/
* SNJ - Sanjay Chaudhry - http://www.suvobi.com/
* SP - Superpin Library - http://getsuperpin.com/
* SPY - Scott Yelich
* SQ - Italic Squirrel, Inc. No released apps yet, but I have (at least) one that's getting close.
* SQU - Squee! Application Development - http://squee.co
* SS - SeoSoft? - http://www.seosoft.info
* SS - Sleepycat Software (I am not affiliated with Sleepycat, but I'm writing wrapper objects for Berkeley DB -- and DB is taken)
* SSS - SunSet Software
* SKP - Skorpiostech - http://www.skorpiostech.com
* ST - Subsume Technologies - http://www.subsume.com/ (Many frameworks, including open source code: http://www.subsume.com/assembled/Source.html )
* STF - stuffonfire.com - http://www.stuffonfire.com -- Don't you wish people would STFU(tilities)? Found in Newspeak, tickr, MFPInterceptor?, commercial stuff...
* STS - Sunrise Telephone Systems - http://www.sunrise-tel.com
* SV - Savanna - http://www.savannax.com
* SVB - Suvobi - http://www.suvobi.com
* SVI - Shawn Van Ittersum - http://www.shawnvanittersum.com
* SW - SamWarmuth? Also ShockWidgets? -?
* SWM - SoftWorks? Media - http://www.softworksmedia.com/
* SX - Stealth Imaging - http://www.stealthimaging.com/ -- H.264 A/V frameworks
* SY - Softyards Software - http://www.softyards.com/
* T2 - Subfuzion, Inc. (founders initials) - iPhone library for social network apps (used by TennyEats) - http://www.tennyeats.com
* TC - Thinking Code Software Inc. - http://www.thinkingcode.ca/
* TF - Tanjero frameworks (for apps like BitPlayer?)
* TF - Tomas Franzén ( tomas@tomasf.se )
* TH - Tom Hartley
* TI - Time Inc.
* TJ - TJSoft?
* TJH - TimHart
* TL - Calf Trail Software - http://calftrail.com / http://code.google.com/u/calftrail/
* TM - ThinkMac Software
* TM - themacuser - miscellaneous. might pop up in KisMac? eventually.
* TMU - themacuser
* TQ - TEQneers GmbH & Co. KG - http://www.teqneers.com
* TR - Triage Software
* TS - Transact! Database Software - Database agnostics for Mac OS X - Visually layout databases, without knowing SQL! - recentChaos - http://www.web-site.net.au/
* TS - Technological Stressings - http://www.technicalstressings.com
* TS - Tangerine Software - http://www.tangerine-soft.de
* TT - Toshiba TAP (Toshiba Australia Pacific)
* TT - Tuparev Technologies - http://www.tuparev.com
* TT - Three20 - Facebook's iPhone UI Library - http://github.com/facebook/three20
* TTT - Mattt Thompson
* TW - ThoughtWorks
* TX - Toxic Software: http://toxicsoftware.com/
* UA - Universal Access (Apple)
* UI - UIKit (Apple)
* UK - UnitKit - http://www.unitkit.org
* ULI - Uli Kusterer's new prefix for all his open-source stuff to avoid UnitKit collision that "UK" now has.
* UN - undefined.org
* UO - UnitOOPS? Software http://www.unitoops.com/
* URV - theunraveler.com
* US2 - http://www.ustwo.co.uk
* UT - LaunchServices functions for working with UniformTypeIdentifiers? (Apple's)
* UW - Used Wheels, objects to get you moving (under construction) http://www.ilike.co.nz/
* UX - n[ui]x Project (Now the MacUX Project; grmartin.me)
* v002 - v002 framework and plugins for Quartz Composer - http://002.vade.info
* V - Valentina database Cocoa framework (C++ wrapper) - http://www.paradigmasoft.com
* VH - VertexHive?
* VK - Vocal Kit - http://github.com/KingOfBrian/VocalKit/ - Pocket Sphinx Speech Recognition Wrapper
* VL - Vinyl, a better music player for OS X.
* VM - Vanille Media (OS X / iOS Developer)
* VN - Vividnoise (iOS Developer)
* VU - Vues Presentation Framework - recentChaos - recentChaos.com
* VX - Valentina database Cocoa native high-performance kernel & EOF, exclusively for Mac OS X - by SKW
* WA - Walker Argendeli - http://www.softworksmedia.com/
* WB - Westbright - http://www.westbright.com
* WI - That's Winnie ( http://thatswinnie.com/ )
* WO - Wincent, WebObjects?
* WS - WaveSense? - http://www.wavesense.info
* WT - Watch Tower
* XI - http://www.xcodeitalia.com
* XM - XVSM Mobile Spaces
* XS - Xargos
* XX - Apple internal experimental
* YL - http://yuppielabel.com
* ZK - ZipKit? http://bitbucket.org/kolpanic/zipkit/
* ZP — [http://zapstreak.com Zapstreak]
* ZY - iTM2 - http://itexmac.sourceforge.net/



Please add more if you think it would be useful. Perhaps your own. ***Yes...***

----

I found that Apple stole my initials!! Anybody knows a good lawyer out there? --NirSoffer

Isn't LS LaunchServices' prefix? --EnglaBenny ( My prefix is EB and I don't understand why YOU have to reserve that)

Yep, it is. Luckily, I don't use LaunchServices. I'm careful. But it's a good demonstration of where a conflict could potentially occur.

-- RobRix (I'd reserve RR, but no, I wouldn't.)

"Luckily, I don't use LaunchServices. I'm careful." This doesn't matter. All you have to do is link launch services and you'll have a problem. You don't even have to link it directly. If you link app kit, and app kit links launch services, and you have names that collide, you'll be in trouble. My guess is that app kit does link launch services. Jon H.

----

We only really need to worry about collisions if we're going to be using someone else's code.  There's a pretty good chance I'll use MOkit or Omni or the MPW stuff, but for some of the smaller developers (HogBay?), I don't think I'll be using a lot of their stuff.  No reason to contort the prefix you want to use just because someone small beat you to it first.

----

If you are only using two letter prefixes thats 26 x 26 = 676 possible combinations. I don't think there are only 676 cocoa developers in the world. Allowing lowercase gives you 2704. 3 letters are better: 26 x 676 = 17576 possible combinations. 4 letters give 456976.

Is there a cocoadev prefix? Then people who want to give ownership of their code to cocoadev could use it. CD is already taken I believe. -- MikeAmy

How 'bout CCD as the CocoaDev prefix?

----Let's be thorough. Uppercase only: two characters-- 676 possibilities; three characters-- 17 576; four characters-- 456 976. Allowing for mixed case gives us 2 704; 140 608; & 7 311 616 respectively. And that's assuming A-Z.  Anyone want to try thsi with UNICODE? heh...

----

Vanity, and the goals of creating brief, readable, and self-documenting code collide fundamentally with namespace safety considerations. Don't they?

There may indeed be fewer than 676 Cocoa developers out there who truly merit their own Leading Uppercase Digraph.
I am not one of those. I suppose I shall call them LUDites, now.

For the rest, when dealing with mundane class names, one ought prudently to insert one's digraph anywhere it fits safely, rather than into the first hole it finds.

----

Just out of curiosity, why are you calling the prefix a digraph?  Is that not short for directed graph?

Consult a dictionary.  A Digraph (literally 'twice writing') can mean "two letters".  You can get pedantic that a digraph is two letters that form a single sound (like 'ph' or 'sp'), but in the sense of a two letter prefix, the term makes a lot of sense.

----

Two letters that form a single sound (phoneme) is usually referred to by "dipthong" (yes, it's a horrible word!).

BTW, "sp" is not a dipthong - it actually consists of two phonemes. But that's getting FEARFULLY pedantic.  ;-)

No DS expert, I was not aware that "directed graph" had been granted an elision to "digraph"; very ill-considered IMO, but so be it.

Those data structures folks should have consulted a dictionary before appropriating it. For "elision", consult a dictionary.

"Digraph" is just so-much-less-cumbersome than "two letter combination", and has some serious semantic priority when used as such.

Especially considering its relevance to the present discussion. 

-- DS (my own digraph, ironically enough)

----

"Bigram" is used in cryptography and statistical text analysis and is unconnected to sound. --AllanBaruz

----

OK, from now on I'm going to use the suggested CCD as the cocoadev prefix. I already have code that I want to give to cocoadev under GNU licence.
see CCDMessageDistributer. -- MikeAmy 

----

Is there a real purpose to this page? I highly doubt someone won't use DB because DevBoys claimed it (here). Have they ever released anything at all? 

Not that I can see.  I think the real purpose to this page is that one-person "shops" can get that thrill of legitimacy. "see!  I'm IMPORTANT because I've staked out my PREFIX!"

----

The purpose of this page is clear:  it allows us to try to avoid name collisions.  If someone wants to use DB then this page will show them that maybe it isn't a good idea.  It also shows who they should talk to about the prefix.  In your DevBoys example, someone who wants to use DB would know of a potential conflict and could ask them if they are actually using it.

So, I think that there is a very real purpose to this page even though it isn't actually enforced (which shouldn't really matter since such name disputes would make the developers look very petty unless they already have a large code-base which uses the names).

----

While I think this page is valuable and relevant, I don't think that name collisions need to be at the top of most Cocoa developers concerns. Ask yourself how many name collisions you would have had if you hadn't used a 2 character prefix. Any at all? I know in my Java development work ( which I do a LOT more of, since I get paid for it... ), I have run into exactly 3 class names that would have had name collisions on projects if it weren't for package names. 1 is in Sun's own java code (java.util.Date and java.sql.Date). The other two were in very large projects I was on. The collisions were between a class we had, and a class in some library. One project had over 100,000 lines of code. The other over 400,000.

So far in my open source code, I've had no cases of name collisions being reported. Might be due to lack of use, might be due to a sufficiently unique name.

I don't mean to belittle the problem of name collisions. It IS a problem. I beleive a language based solution should be created. It is by far the cleanest way to manage a language based problem.

In practice, I'm not too worried about it. The problem is more significant for framework developers than those that consume frameworks in order to deliver apps. So far I don't think we're close to having 676 Framework developers to worry about. We may very well one day..

In my non-framework code, I don't bother with prefixes.

--TimHart

----

anyone else feel that the list on this page could use to be split up into prefixes to **definitively** avoid because Apple uses them (NS*, CF*) and prefixes which are simply 'reserved' by 3rd party developers?

*Not really.  Only the third party prefixes are interesting, because the Apple prefixes are documented elsewhere (and mostly well known).*

----

Removed BartasTechnologies (BT) - I anticipated releasing a framework containing my own tricks, shortcuts, and other unsavory Cocoa-coercions, but have yet to do so. ;-) Couple that with the discussion here, I'm convinced it's not really worth staking out my own claim to a two-letter prefix anyway, growling  jealously at passers-by ...

----

Apple provide clear documentation on class naming and advice on avoiding namespace collision. This issue only affects code segments that co-exist within a common process. Such relationships include plug-ins, frameworks, and injected code.

Preventing Name Conflicts:

http://developer.apple.com/documentation/UserExperience/Conceptual/PreferencePanes/Tasks/Conflicts.html

Code Guidelines:

http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html

There is no formal or enforced register of restricted prefixes. Nor is there any need.

Unless you are developing a code segment destined to exist within a shared environment - and most of us are not dealing with this special case - you do not need to overly concern yourself with namespace issues. Focus on building a great application with a fantastic user experience. -- GCM

----

I just added a bunch of Apple prefixes to the list: LaunchServices (LS and UT), CoreGraphics (CG), CoreServices (CS, with examples), Enterprise Objects Framework (EO), CoreImage (CI), CoreVideo (CV). --*boredzo*

----

Added BitSpinn.org (Bso) - most of my frameworks will be GPL'ed or have a public API -- "shirk"

----

Removed Myko Games; it's dead.

----

Changed Skorpiostech from ST (which is used by the OCUnit stuff) to SKP (SKT was Sketch, thanks boredzo!). 

----

Apparently "Ninja Kitten" won't learn the concept of **alphabetical order** until he/she becomes a "Ninja Cat" ... fixed the list for him/her.

----

Refactored Bso to just BO (BitObjects)-- "shirk"

----

Allowed myself to add my prefix (BTH), since I sometimes publish some APIs. -- Tomek Wójcik.

----

A more comprehensive listing of prefixes used by Apple can be found here:

http://developer.apple.com/mac/library/documentation/MacOSX/Conceptual/OSX_Technology_Overview/SystemFrameworks/SystemFrameworks.html

----

Added myself (for privately released stuff) and my company Harpander to the list.
I hope I don't ask take too much from so relatively small name space :). -- Harry Flink

----

Added EG for Exit Games  -- Kaiserludi

----

Added ZP for Zapstreak -- Cz4rek

--- 

Added FES for Frisky Electrocat Studios and MFR for Madefire -- Danimal

---

Added DB (Dropbox SDK) and GT (Objective Git). I'm not behind either of these projects but use both of them and figured they should be mentioned here. -- User:Peterkelly|Peter Kelly

