---
layout: page
title: AirTunes
---

AirTunes: http://www.apple.com/airportexpress/features/airtunes.html

The Airtunes 2 protocol is in the process of being described:
http://git.zx2c4.com/Airtunes2/about/
http://blog.jasondonenfeld.com/332

Allows streaming from iTunes to an AirPortExpress, which has audio-out which can then be connected to a stereo.

Does the AirPortExpress use Rendezvous to announce an audio streaming service on the LAN? So in theory other programs could a) use this service and b) announce similar services (in case you wanted your audio sent elsewhere)?

*The AirPortExpress Base Station publishes a Rendezvous service of type _raop._tcp., i.e. the RemoteAudioOutputProtocol. See AirTunesRendezvous for details.*

Will System Preferences allow the Sound output to go to such a service?

*Do not think so, iTunes only as the protocol is probably iTunes's custom streaming protocol*

What about passwords? It can't be that anyone with a Mac and AirPortExtreme card can stream to any AirTunes services in the area?

*None that I know of. Yes you could high-jack airport expresses if you so desired*

*You can configure an airtunes password; this prevents anyone without the password from connecting to an AirTunes service to stream to it.*

It probably has similar / same setups controls as the base stations, which includes passwords on the network and limiting connections to specified machines.

----

From looking through the output of 'strings iTunes' on v4.6, it appears to just use RTSP/RTP. Although they added an encryption library too, so it might all be encrypted so you can't easily hijack it. And it does use Rendezvous (_raop._tcp.).

From dumping the protocol exchange, the iTunes<>express protocol is a modified RTSP+SDP, with a challenge/response from iTunes to make sure that it's an airport express.  It uses ANNOUNCE, SETUP, RECORD RTSP methods, and extended attributes in SDP to configure the encryption.  I can't tell if it's a bug or just mean error reporting, but if you supply an RTSP ANNOUNCE with an SDP payload that doesn't configure encryption, it simply closes the connection.  In addition, it runs RTSP on port 5000; the standard RTSP port is 544 so it's clearly not intended to be "really" RTSP.

----

Someone should put together a software AirTunes client... and it would be great if it was maybe cross platform, or at least OS X and 9.

*This would involve reverse-engineering the encryption and keys used between iTunes and AirTunes.*

* You mean these rev-eng efforts? http://mailman.videolan.org/pipermail/vlc-devel/2009-July/063000.html http://mailman.videolan.org/pipermail/vlc-devel/2008-December/054010.html *

FYI, the Airfoil software has companion software called "Airfoil Speakers" which does act as a software airtunes client (well, it's an _Airfoil_ client, same diff.) It's available for just about any platform, even iPhone/iPod touch. You can output audio to your own computer, to airtunes devices like airport express, and to other computers all at the same time.

Référencement en Suisse: http://www.egga-referencement.ch (site officiel développement popularité)

A note on the AirPort private key:
There is a common misconception about this, in this document and elsewhere.  As far as I know, nobody (not DVD Jon or Nanocrew) has prized the key out of the Airport Express which it uses to authenticate itself to iTunes.  What they did is the opposite -- now various softwares can authenticate themselves to the Airport as iTunes, but the iTunes program from Apple still refuses to speak AirTunes to any device that does not authenticate itself as an Airport Express.

What is needed is for someone to disassemble the contents of an Apple Airport Express Firmware Update, to find the keys, and ideally work out the details of the challenge/response key computation.  Or else an enterprising hardware hacker could get the same information out of a running Airport.

Once this was done, it'd be straightforward to write some package that simulates the Airport Express AirTunes behaviour on your favorite piece of non-Apple hardware.  I'm very keen to work on this but the hardware hacking portion is way over my head.

-m-

----
There is a program. It is called oAEP a program that connects to an Airport Express wirelessly and plays music and sound from Windows.

You can use any player you want, Winamp, media player�

Find it here:
http://www.ovesens.net/oAEP
----

----
There are dozens of programs that do that, but none that do what i've described above: allow any computer to pretend to be an Airport Express.
----

----

I bet someone could sell a well-written shareware product that reroutes all the system audio to an AirPortExpress for a nominal price ($7 - $15?). I'd happily purchase both the software and an AirPortExpress (I don't really want one unless I can play all my system audio wirelessly).

----

Actually, something along these lines has been authored. Rogue Amoeba has a slightly more expensive option they call Airfoil, http://www.rogueamoeba.com/airfoil/index.php. It allows any system audio to be redirected to an AirPortExpress.

--Herman

----

----

At the launch of the european ITMS keynote, Steve said that the signal was encrypted to satisfy the record business -- so most likely it will only be usable by Apple applications.

----

The AEX *does* *indeed* have an optional AirTunes password in the admin UI.

----

Encryption has been cracked: http://www.oreillynet.com/pub/wlg/5407 | http://nanocrew.net/blog/apple/revairtunes.html

Bigger article: http://www.theregister.co.uk/2004/08/12/airport_express_cracked/

----

I am able to stream arbitrary audio to an Airport Express without breaking any encryption. Here's how (for Windows, similar applications exist for Linux and Mac OS X)

1) Run WinAmp with the Shoutcast broadcasting plugin. This allows you to stream audio, including system audio and soundcard output, to ...

2) A Shoutcast server - you can run one locally or on another machine.

3) Run iTunes and connect to the Shoutcast stream.

4) Tell iTunes to send the audio to the Airport Express.

--Pat / zippy@cs.brandeis.edu

----
To stream from Winamp or Media Monkey to the Airport Express without having to launch an external program, try:
http://emilles.dyndns.org/software/out_apx.html
----

----
I'd like to be able to stream to my "home theater" Linux box which sit next to the TV and the stereo..
Any hints about feasibility/legality  and the AppleLosslessEncoding ?

*That's not the only problem, you would also have to hack the Apple-Challenge / Apple-Response scheme.*

"Actually, that has already been cracked. See the el reg article, nanocrew, etc."

(Note: actually, that part has *not* been cracked yet.  I wish it was.  See note on the AirPort private key, above.  -m-)

 The problem that you won't be able to solve is that Apple Lossless Encoding is a codec that is not available on the Linux platform, so you'll have an audio stream that you have no way of decoding. You'd be better off with Network Audio Device, which streams to an EsounD.

*I can't find anything on how the Apple-Response is generated though, which is crucial information if you want to write an AirTunes server. Any client can stream to an AirPortExpress because those fields are optional for the AirPortExpress, but iTunes will not stream to anything not responding with the right Apple-Response.*

**Check AirTunesEncryption for information on that**

As far as I know, mplayer ( http://www.mplayerhq.hu/ ) has a modded windows codec that can do the MPEG4 decoding -- macTijn

What if one would like to stream from iTunes to a MacOS X box? i have a mac connected to my stereo. the mac should be capable to decode the lossless codec. has anybody tried things like this yet?

----

Why can't you just use the Advanced >> Open Stream utitlity inside of iTunes to connect to whatever you want to??
Can't AirTunes accept any signal coming from iTunes?

ChrisW


What I meant to say was  "Yeah, like Pat said" ;)

----

Just wanted to point out that Rogue Amoeba has announced their Slipstream software for Mac OS X. It allows you to stream sound from any Mac OS X application to your Airport Express.

http://www.rogueamoeba.com/slipstream/

- MarkDavis

----

Is there a Airtunes-Linux-Server project? I want to play from my iTunes-Client directly on my Linux-Box running daapd so far. 

- JB

Yes, there's a project here:

http://sourceforge.net/project/showfiles.php?group_id=119473&package_id=130187

Although I believe the stumbling block for non-quicktime platforms is that the Apple Lossless codec isn't available.

NOTE: this project looks like another open-source client for streaming data to a real Apple AirPort, not an
AirPort simulator.  It has features for working directly from the database of an attached iPod.  
-m-

----

- Guyzmo

"Just to add my 2 cents... Wouldn't it be possible, and even better to redirect the sound output at the Darwin level through the
network to another unix box ? I don't think it would be terrible to code, it just needs time to find all the correct documentation..."

i played around with this, using Network Audio Device under OS X 10.3 streaming to E sounD on a Sharp Zaurus, and it basically
worked, except that the Zaurus only does 802.11b and couldn't get enough bandwidth for the stream.  but it ought to work
on a faster network.

-m-


----

- RanH

"A quite different approach - would you think its possible to run AirTunes without an AirPortExpress at all? More specifically, would it be possible to emulate an AirPortExpress on a Windows PC?

As many people who made the switch, I have a very large black box at home that I hardly use anymore - but I still need it to run Windows cause sometimes there's things I can't do on the mac. Is there any way to turn that big black box into a small white AirPortExpress? The end result would be something like runing netTunes, but without all the extra hardware"

See my note on the Airport Private Key, above.  Once that's solved, then the ideal (for me) would be a bootable CD that you can stick in your Windows machine, which boots Linux, configures the sound and network cards, and then starts an AirTunes server, advertising it with Rendezvous.  I would love to work on this.
-m-

----

The "Airport Express Client" (raop-play) is a Client solution, but what i need is a Server! I would like to play music over the Airtunes feature inside my iTunes - on my remote computer which is running daapd. I am aware of forwarding a "network-soundcard" but this is not so comfortable as with iTunes.  :)
any suggestions?

- JB

*It is being worked on.  See http://aquaticmac.com/fairport/ and http://aquaticmac.com/blog/archives/2 for how you can help.*

The above links have been removed.  See the following dicussion for further progress, via a program called pinhead 
http://partiallystapled.com/blog/category/software/

----
AppleLosslessEncoding has been reverse engineered, MPlayer can now decode this on Linux or other platforms. 

----

Why not use http://www.rogueamoeba.com/airfoil/
Seems to solve your problems for $25.  I don't have anything to do with this product, but it makes sense to me.
Rob

----

What I REALLY want is the ability to send the same stream to multiple AirPort Express boxes.  For that same music all over the house effect. Rob

----

You're in luck! Airfoil 2 has been announced, and will support multiple Airport Expresses simultaneously. See: http://www.rogueamoeba.com/utm/posts/News/airfoil-2005-10-07-12-00

----

----

I bet someone could sell a well-written shareware product that reroutes all the system audio to an AirPortExpress for a nominal price ($7 - $15?). I'd happily purchase both the software and an AirPortExpress (I don't really want one unless I can play all my system audio wirelessly).

----

playing *all* your system audio wireless is a great dream to have, but what I've found at this point is that FM transmitters / receivers are much more robust.  
most importantly is the fact that broadcasting audio data over a WLAN introduces a significant amount of latency (ie delay).  I have experienced 
with my airtunes systems latencies of up to 3 seconds.  you are *NOT* going to have fun watching any movie with a delay in the audio measurable in seconds.
which implies that if you want to use an airport express based wireless home system for theatre you need to roll your own video delay unit as well.
of course for just listening to music, (ala iTunes) a large latency is not a big deal, I guess very few people have even noticed at this point.  I would imagine,
however, that if you could transmit from iTunes to more than one airPort express simultaneously, that they would not be in sync.  FM would be in sync.
both FM and WLAN have problems with interference, though in my opinion WLAN interference is less 'natural' sounding because when there is dropout, 
*all* of the music will still get played (ie the timing of the music is altered-- the beat is dropped-- which makes alot of people like me go crazy).  
an interference-less world would be nice.  

I just want to say that I am a very big fan of the airport express and the possibilities it has opened for me. for example, now i have a very cheap computer
with a very big hard-drive connected to my router (via ethernet) running only iTunes.  all of my CD's are encoded there and are available over WLAN throughout
my house.  with various airport express's and laptops, etc... i can access my whole music library and play it where i need it.  this is great.   

but finally, to get back to the programming side of things, I don't see a nominally priced audio application being viable...
if you want to sync to video there are topics there which get you out of the $7-$15 range very quickly.  
a video to audio offset of 10ms is already very unacceptable to viewers.  how would you measure the audio latency over the 
WLAN (which will be different on every connection)  *and* be able to compensate for interference and lost packets
maintaining a television-quality viewing experience?  in this case you need to send the audio and the video packets together. 
you need wireless A/V.      |K<

----

----

What I REALLY want is the ability to send the same stream to multiple AirPort Express boxes.  For that same music all over the house effect. Rob

----

Just in case anyone doesn't know, as of iTunes 6.0.2 (yes, the one that added the mini-store), you can stream to multiple AirPort Expresses and your computer speakers all at the same time supposedly (from one copy of iTunes of course).  I've only got one Airport Express, but I can play music in iTunes on my computer and my Airport Express at the same time.  There doesn't seem to be that much of a delay, but I don't know how well it scales.  If someone else has more than one Airport Express and wants to report back, I'd be interested to hear the results in case I ever want to buy multiple Airport Expresses.

We have multiple Airport Expresses at home and we've done a test to see if they would sync well enough. To be honest, we both couldn't hear a discrepancy (our rooms are next to each other). It really is a 'same music all over the house effect'. Pretty impressive! -MJH

----
I couldn't figure out how to get it to play on the computer and express at the same time, how'd you do that?

I just got 6.0.2 and my second express. I tried getting it to play from both expresses but I don't have a second set of speakers handy so I'm not sure either way. My first try I had both setup as individual base stations and in order for iTunes to see it you have to be connected to it - select one, start playing, connect to the other and the first stops. Selecting the newly connected one in iTunes made iTunes beachball, bad enough that I had to force quit.

I reconfigured the second express as a "remote base station" but without a second set of speakers handy I can't tell if it's playing or not. It would be insanely cool if it JustWorks like that but for some reason I doubt it does.

----

Here's the Mac OS X hint about it:  http://www.macosxhints.com/article.php?story=20060111174136912

Apple also lists it as an AirTunes enhancement: http://www.apple.com/itunes/download/ (note that it says up to 3 powered speakers)
----
Besides iTunes 6.0.2 you'll need Airport Firmware 6.3  http://www.apple.com/support/downloads/airportexpressfirmwareupdate63formacosx.html

----
I finally got a second set of speakers and this is just amazing. If there's latency between the base stations I can't hear it. I may end up with a third base station before too long. Did I mention that it was amazing?..ok, just checking.

----
*I have experienced 
with my airtunes systems latencies of up to 3 seconds. <snip>  I guess very few people have even noticed at this point. *
I listen to iTunes on directly connected speakers and hear the same music playing in the living room over AirTunes. No noticeable delay. There is a sync time before the music starts playing. 
Even in a setup with 3 expresses i didn't hear a noticeable delay.

Great project idea, the AirTunes client.
No real programming skills here, but i am a student in graphics design ;)
----
Lemme just summarize discussion so far, and clear up a few things.
Airfoil, from Rogue Amoeba, facilitates streaming of any app, audio input, and system audio to AirPort Express. Those hoping to stream system audio this way all the time will be disappointed. There is significant latency, up to 5 seconds. Both Airfoil and iTunes compensate for this, delaying all outputs (even local audio, in iTunes' case) to match the "weakest link."
As of yet, there is no solution to emulate an APx. Its half of the key appears to be unknown, and it makes sense that this would be better protected if the music industry's interests were in mind. (A device receiving music might record it, a device sending it already has it.) Add me to the crowd looking to overcome this, but without the equipment or resources to solve it myself.
A low-level audio transmission system used in place of the AirTunes protocol would be great, but it must either be very low lag or able to delay local audio based on network latency. That seems to be all at the moment.
-Sidney
----
Another idea would be to get an image of the Airport Express OS and virtualize/emulate it, then send the audio output throug the computer speakers...

----
As far as I have been able to tell, the Airport Express runs some version of VxWorks: http://en.wikipedia.org/wiki/VxWorks .
I had a look around trying to find some kind of emulator, or just documentation, but came up empty.  They don't seem to release much to the non-paying public.  But there are VxWorks developers out there somewhere who must know something about how to approach this ...  -m-

----
I believe that would count as DirtyRoom ReverseEngineering, which is illegal in many jurisdictions. Proceed with caution!

----
It would only be illegal if you redistribute the OS image which is covered by Apple's copyright.

----
No, that's not reverse-engineering at all. That's just running an OS on non-supported hardware.  I don't doubt that Apple would find it illegal, but they're not busting people for running MacOS on cheap Intel boxes, so why would they bother with this? -m-
http://www.badcreditwhiz.com/topbadlenders.html

----
Is it not possible to analyse the new Apple TV soft as it seems to provide Airtune functions?
The private key should be somewhere in the apple TV no?
Am I wrong?
somm15_at_yahoo.fr

----
The AppleTV appears to use a completely different authentication scheme based around FairPlay, so this is likely to be an even more difficult problem.

----
Very annoying, I don't have the knowledge to try to open my AEX and extract the private key.

----
Maybe it would be easier to patch itunes to skip key check? I would not mind if mine would be patched to work with open-source speakers server

----
The key has been extracted http://mailman.videolan.org/pipermail/vlc-devel/2011-April/079616.html http://www.mafipulation.org/blagoblig/2011/04/08#shairport

----
https://github.com/albertz/shairport

