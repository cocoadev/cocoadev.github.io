---
layout: page
title: RemoteAudioOutputProtocol
---

This is a variant of the RealTimeStreamingProtocol (RTSP), but it seems to be on TCP ports 5000 (control) and 6000 (data) instead of the RFC ports. The iTunes app connects to the RTSP server on 5000, tells it to record an Apple Lossless stream, tells it an RSA key, and then switches to 6000 to send the data.

*It defaults to 6000, but the airport tells the client the port to use in the RTSP SETUP exchange; I've had it use 6001.*

It's RSA, so the algorithms are well-known. I think anyone who knows enough about RSA to write an ssh client/server pair could write an AirTunes client - it should be much less work that, say, DVD's CSS, since the RSA algorithm is well-known. 

----

See AirTunesEncryption for encryption details.

----

The trick would be to have a known plaintext, so you know when it's working right - luckily, you could do that by recording a short sample as "Apple Lossless", then record what iTunes sends to the AEX with tcpdump, and then test your program until it generates the same bytes,

Here's what goes over the wire(less) on port 5000 - note the challenge/response and RSA info.  (Everything indented is part of the same line; no spaces.)

    
ANNOUNCE rtsp://10.0.1.101/3172942895 RTSP/1.0
C**'Seq: 1
Content-Type: application/sdp
Content-Length: 567
User-Agent: iTunes/4.6 (Windows; N)
Client-Instance: 9FF35780A8BC8D2B
Apple-Challenge: 09KF45soMYmvj6dpsUGiIg

v=0
o=iTunes 3172942895 0 IN IP4 10.0.1.101
s=iTunes
c=IN IP4 10.0.1.103
t=0 0
m=audio 0 RTP/AVP 96
a=rtpmap:96 AppleLossless
a=fmtp:96 4096 0 16 40 10 14 2 255 0 0 44100
a=rsaaeskey:5QYIqmdZGTONY5SHjEJrqAhaa0W9wzDC5i6q221mdGZJ5ubO6Kg
            yhC6U83wpY87TFdPRdfPQl2kVC7+Uefmx1bXdIUo07ZcJsqMbgtje4w2JQw0b
            Uw2BlzNPmVGQOxfdpGc3LXZzNE0jI1D4conUEiW6rrzikXBhk7Y/i2naw13ayy
            xaSwtkiJ0ltBQGYGErbV2tx43QSNj7O0JIG9GrF2GZZ6/UHo4VH+ZXgQ4NZvP/
            QXPCsLutZsvusFDzIEq7TN1fveINOiwrzlN+bckEixvhXlvoQTWE2tjbmQYhMvO
            FIly5gNbZiXi0l5AdolX4jDC2vndFHqWDks/3sPikNg
a=aesiv:zcZmAZtqh7uGcEwPXk0QeA

*RTSP/1.0 200 OK*
*C**'Seq: 1*
*Apple-Response: u+msU8Cc7KBrVPjI/Ir8fOL8+C5D3Jsw1+acaW3MNTndrTQAeb/a*
*                5m10UVBX6wb/DYQGY+b28ksSwBjN0nFOk4Y2cODEf83FAh7B*
*                mkLpmpkpplp7zVXQ+Z9DcB6gC60ZsS3t98aoR7tSzVLKZNgi2X2sC+vGsz*
*                utQxX03HK008VjcdngHv3g1p2knoETd07T6eVfZCmPqp6Ga7Dj8VIIj/GEP3*
*                AjjDx3lJnQBXUDmxM484YXLXZjWFXCiY8GJt6whjf7/2c3rIoT3Z7PQpEvPmM*
*                1MXU9cv4NL59Y/q0OAVQ38foOz7eGAhfvjOsCnHU25aik7/7ToIYt1tyVtap/kA*
*Audio-Jack-Status: connected; type=analog*

SETUP rtsp://10.0.1.101/3172942895 RTSP/1.0
C**'Seq: 2
Transport: RTP/AVP/TCP;unicast;interleaved=0-1;mode=record
User-Agent: iTunes/4.6 (Windows; N)
Client-Instance: 9FF35780A8BC8D2B

*RTSP/1.0 200 OK*
*C**'Seq: 2*
*Session: 80B6E4C0*
*Transport: RTP/AVP/TCP;unicast;interleaved=0-1;mode=record;server_port=6000*
*Audio-Jack-Status: connected; type=analog*

RECORD rtsp://10.0.1.101/3172942895 RTSP/1.0
C**'Seq: 3
Session: 80B6E4C0
Range: npt=0-
RTP-Info: seq=40701;rtptime=2135662502
User-Agent: iTunes/4.6 (Windows; N)
Client-Instance: 9FF35780A8BC8D2B

*RTSP/1.0 200 OK*
*C**'Seq: 3*
*Audio-Jack-Status: connected; type=analog*

SET_PARAMETER rtsp://10.0.1.101/3172942895 RTSP/1.0
C**'Seq: 4
Session: 80B6E4C0
Content-Type: text/parameters
Content-Length: 18
User-Agent: iTunes/4.6 (Windows; N)
Client-Instance: 9FF35780A8BC8D2B

volume: 0.000000

*RTSP/1.0 200 OK*
*C**'Seq: 4*
*Audio-Jack-Status: connected; type=analog*

FLUSH rtsp://10.0.1.101/3172942895 RTSP/1.0
C**'Seq: 5
Session: 80B6E4C0
RTP-Info: seq=40792;rtptime=2136035238
User-Agent: iTunes/4.6 (Windows; N)
Client-Instance: 9FF35780A8BC8D2B

*RTSP/1.0 200 OK*
*C**'Seq: 5*
*Audio-Jack-Status: connected; type=analog*

TEARDOWN rtsp://10.0.1.101/3172942895 RTSP/1.0
C**'Seq: 6
Session: 80B6E4C0
User-Agent: iTunes/4.6 (Windows; N)
Client-Instance: 9FF35780A8BC8D2B

*RTSP/1.0 200 OK*
*C**'Seq: 6*
*Connection: close*
*Audio-Jack-Status: connected; type=analog*



----

When the AirTunes has a password configured for the speakers, it uses HTTP Digest Authentication (RFC 2069) -- **almost**.  What follows is a dump of the exchange with iTunes when the password is "T**'uneItUp".

    
OPTIONS * RTSP/1.0
C**'Seq: 1 
User-Agent: iTunes/4.6 (Macintosh; N; PPC) 
Client-Instance: 78C152DD8C2636B2

*RTSP/1.0 401 Unauthorized*
*CSeq: 1*
*WWW-Authenticate: Digest realm="raop",*
*  nonce="8A000000F689AF0BBD1A871E728A0116EE7C5933"*
*Audio-Jack-Status: connected; type=analog*

OPTIONS * RTSP/1.0
C**'Seq: 2 
User-Agent: iTunes/4.6 (Macintosh; N; PPC) 
Client-Instance: 78C152DD8C2636B2
Authorization: Digest username="iTunes", realm="raop",
  nonce="8A000000F689AF0BBD1A871E728A0116EE7C5933",
  uri="*",
  response="3C75D311A6CFA978209DD7AA3584EAC0"

*RTSP/1.0 200 OK*
*C**'Seq: 2*
*Public: ANNOUNCE, SETUP, RECORD, PAUSE, FLUSH, TEARDOWN,*
*  OPTIONS, GET_PARAMETER, SET_PARAMETER*
*Audio-Jack-Status: connected; type=analog*


Unfortunately, this implementation of Digest Authentication uses the alphabet **0123456789ABCDEF** instead of RFC 2069's **0123456789abcdef**.  The difference is significant, since you take a hash of the hash.

----

The volume control appears to be in negative units; the 0.000000 seen in the first dump is "full volume".  In a trace where I was moving the volume control relatively smoothly all the way down, there was a big jump from -29 to -144.0000.  Presumably -144 is "off".  A couple of sample RTSP exchanges setting the volume:

    
SET_PARAMETER rtsp://10.0.1.2/3174464733 RTSP/1.0
C**'Seq: 30
Session: 80C18FE0
Content-Type: text/parameters
Content-Length: 20
User-Agent: iTunes/4.6 (Macintosh; N; PPC)
Client-Instance: 78C152DD8C2636B2

volume: -29.091133

*RTSP/1.0 200 OK*
*C**'Seq: 30*
*Audio-Jack-Status: connected; type=analog*

SET_PARAMETER rtsp://10.0.1.2/3174464733 RTSP/1.0
C**'Seq: 31
Session: 80C18FE0
Content-Type: text/parameters
Content-Length: 21
User-Agent: iTunes/4.6 (Macintosh; N; PPC) 
Client-Instance: 78C152DD8C2636B2

volume: -144.000000

*RTSP/1.0 200 OK*
*C**'Seq: 31*
*Audio-Jack-Status: connected; type=analog*


Later, bringing the volume all the way back up returned to *0.00000*.

*Presumably the volume is attenuation in dB.*

----

Jon Lech Johansen published sample code in C# that lets you stream MPEG4 Apple Lossless files to your AirPort Express:
http://nanocrew.net/blog/apple/revairtunes.html

----

Can anyone with an AirPort Express and iTunes 4.7.1 (as opposed to 4.6, which is used above) log the exchanges for a non-passworded connection?  Thanks.

----

Q: How were you able to capture the text during the AirportExpress<->Mac exchange?

A: Use wireshark (http://wireshark.org) or another packet sniffer.

