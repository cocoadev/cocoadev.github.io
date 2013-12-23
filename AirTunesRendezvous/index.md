---
layout: page
title: AirTunesRendezvous
---

AirTunes uses Bonjour to announce remote speakers to the network. To browse for available AirTunes instances, type:
    
mDNS -B _raop._tcp


To look up a specific instance, type (<name> is the instance name):
    
mDNS -L <name> _raop._tcp

----
The Bonjour service has following attributes, according to the mDNS command line tool. 


The service **name** is something like *00112400B28F@Speakers*. The first part up to @ seems to be the Base Station's MAC address (*00:11:24:00:B2:8F* in this case). The latter part is the speaker's name (*Speakers* in this case).

The service is of **type** *_raop._tcp* (RemoteAudioOutputProtocol).

The **port** is 5000 (TCP).


The **TXT records** are *txtvers=1 vn=3 pw=false sr=44100 ss=16 ch=2 cn=1 et=1 ek=1 sv=false sm=false*


*txtvers=1

Version number of this TXT record.

*vn=3

*unknown*, maybe some kind of sub-version (vn = versionnumber?).

*pw=false

Is the AirPortExpress password protected?


*sr=44100

Sample rate = 44100 Hz.

*ss=16

Sample size = 16 bits

*ch=2

2 Channels = stereo.

*cn=1

*unknown*, maybe Audio-Jack-Status (cn = connected?)

*et=1

*unknown*, maybe et = ethernet? Or encryption type?

*ek=1

*unknown*. Or encryption key (present)?

*sv=false

*unknown*

*sm=false

*unknown*



----

When publishing a Bonjour service with the above details iTunes connects to the given port (5000 by default). The RemoteAudioOutputProtocol is used for communication. If your TXT records are corrupt you will end up with  this message:


*The remote Speaker "..." is not compatible with this version of iTunes.*

----

when i use the above info to advertise remote speakers it tells me the remote speakers are in use. i'm using 
    
mDNS -R 0030651c20b9@Bunk _raop._tcp. local 5000 txtvers=1 vn=3 pw=false sr=44100 
    ss=16 ch=2 cn=1 et=1 ek=1 sv=false sm=false

could someone grab the txt record for an APX that's not in use? it should help to figure out more of it.

I'm new with this, so please bear with me... If the above error line comes up, what do you do to repair your corrupted TXT record?

----

This makes my iTunes see a set of speakers named Relax - Of course it fails to connect, but no error message is given.
    
mDNS -R 0030651c20b9@Relax _raop._tcp. local 5000 txtvers=1 vn=3 pw=true sr=44100
    ss=16 ch=2 cn=0,1 et=0,1 ek=1 sv=false sm=false tp=TCP,UDP

Update: I grabbed the string from an Airport Express that wasn't in use ;-)


----


Did you ever get this to work?  I was able to duplicate your results, using mDNS and Network Beacon, but I get stuck on the problem that it won't connect and gives no error.  

I'm trying to get iTunes to connect to an Airport Express on a different subnet.   I've tried using SSH to tunnel the connection (on port 5000) from a machine on this net to the APE on the other network. I've also tried creating a proxy mDNS entry that points to the APE's IP on the other network.  Neither seem to work.


Any help would be appreciated, and I can be contacted at job1729@gmail.com


----


I've been looking at this to figure out why the old ATV doesn't work with AirPlay, when the Airport express does... The key mDNS keys are ek and et.

The AppleTV broadcasts   et=0,2
The AirportExpress broadcasts:  ek=1 et=0,1

My guess is that these are Encryption Key (present) and Encryption Type, but that's only a guess. The mDNS docs which are supposed to define the keys simply tell you to contact apple (helpfull).

Of course there are other differences, but broadcasting the ATV keys with ek and et changed makes makes the fake source appear on an iPod (iTunes itself doesn't mind so much whats in these fields).

ek can be 0 or 1 - it just has to be there.

et can be 1,3,4 or 5

Presumably 3,4 and 5 are new types used by the new ATV, and/or 3rd part airplay servers.


Here are the commands:
    
#Airport Verision
#mDNS -R 0030651c20b9@Hack _raop._tcp. local 5000 cn=0,1 ch=2 ek=1 et=0,1 sv=false Version=1 da=true Sample\ Rate=44100 ss=16 Password=false vn=65537 tp=TCP,UDP vs=102.2 am=AirPort4,107 fv=75200.14 sf=0x5

#ATV version
#mDNS -R 0030651c20b9@Hack _raop._tcp. local 5000 cn=0,1 ch=2 ek=0 et=0,2 sv=false Version=1 da=true Sample\ Rate=44100 ss=16 Password=false vn=65537 tp=TCP,UDP md=0,1,2 vs=102.2 am=AppleTV1,1 sf=0x4

#atv version with minimal changes
 mDNS -R 0030651c20b9@Hack _raop._tcp. local 5000 cn=0,1 ch=2 ek=0  et=1 sv=false Version=1 da=true Sample\ Rate=44100 ss=16 Password=false vn=65537 tp=TCP,UDP md=0,1,2 vs=102.2 am=AppleTV1,1 sf=0x4


It would be interesting to see if broadcasting these parameters could trick an iPod into broadcasting to a ATV - if the block on the old tech is just at the discovery stage (probably wishful thinking).

also if anyone with an atv2 could see what ek/et settings it's advertising that would be usefull.

----

Here is AppleTV V2 as seen by Avahi:

    
 avahi-browse --terminate --all --resolve --parsable | grep -i appletv
+;eth0;IPv4;40C7B59B5D7EADAA;_appletv-v2._tcp;local
=;eth0;IPv4;5855CA0EC672\064Apple\194\160TV;AirTunes Remote Audio;local;AppleTV.local;192.168.1.112;49152;"sf=0x4" "am=AppleTV2,1" "vs=104.29" "md=0,1,2" "tp=TCP,UDP" "vn=65537" "pw=false" "ss=16" "sr=44100" "da=true" "sv=false" "et=0,3" "cn=0,1" "ch=2" "txtvers=1"
=;eth0;IPv4;Apple\194\160TV;_airplay._tcp;local;AppleTV.local;192.168.1.112;7000;"model=AppleTV2,1" "features=0x7" "deviceid=58:55:CA:0E:C6:72"
=;eth0;IPv4;40C7B59B5D7EADAA;_appletv-v2._tcp;local;AppleTV.local;192.168.1.112;3689;"MiTPV=196611" "DFID=2" "PrVs=65538" "Name=Apple�TV" "fs=2" "MniT=167845888" "hG=00000000-05bf-a709-e555-9474a09b4015" "txtvers=1"
 

HTH, Andrej                         

----
Thanks that confirms what I suspected - atv2 is broadcasting et=0,3.

Here's the command to create a dummy atv2 airtunes mDNS broadcast.
    
mDNS -R 0030651c20b9@Hack _raop._tcp. local 5000 cn=0,1 ch=2 et=0,3  sv=false da=true sr=44100 ss=16 pw=false vn=65537 tp=TCP,UDP md=0,1,2 vs=104.29 am=AppleTV2,1 sf=0x4
 
some other minor things have changed but if we assume et is encryption type, it uses the new type 3 (presumably 0 is clear text). Interestingly theres no ek entry. It's been shown that airtunes (video) uses fairplay based drm rather than public key, so perhaps et=3 doesn't use a key in the same way type 1 does.

----

Here is what worked for me with Network Beacon:

    
Service Name:	YourMACaddress@yourSpeakerName
Service Type:	_raop._tcp.
Port Number:	5000

Text Record:

txtvers=1\001ch=2\001cn=0,1\001ek=1\001et=0,1\001sv=false\001da=true\001sr=44100\001ss=16\001pw=false\001vn=65537\001tp=TCP,UDP\001vs=102.2\001am=AirPort4,107\001fv=75200.14\001sf=0x4

Enable Host Proxy:	checked
Host Name:		your hostname of Airfoil Speaker
IP Address:		IP address of Airfoil Speaker


You can figure out the right Text Record by using the "Bonjour Browser" in your remote subnet.

