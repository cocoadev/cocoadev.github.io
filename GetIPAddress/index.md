---
layout: page
title: GetIPAddress
---

hey, i am trying to get the ip address of a computer...i dont meen the local address, eg 10.0.0...i meen the wan address, eg 654.456...

thanks,

jeremy

----

umm.... good luck finding a ip starting with 654.456....... ;)

/ptx

----

=P it was an example. i was just trying to give an example of a wan? ip.

jeremy

----

Not all computers have their own WAN address, do they? I.e. if you're using a gateway server, won't the WAN address of all computers it's routing traffic to be the same as its own WAN address? -- RobRix

----

thats correct. but the only reason i wanted it was so i could use it as an easy way to get my ip, as i am not using a gateway server.

jeremy

----

Yes, I also need to get the WAN IP of a computer, whether or not they are behind a router or another kind of network setup. The only way I can think of is parsing http://whatismyip.com with PCRE for Cocoa. But that web server could easily go down which could cause some major problems. Is there perhaps a command-line program that I could open an NSTask to that gives the computer's WAN IP address?

-- MarcWeil

----

You could write a small Perl script and execute it through NSTask I think

----

Your computer could have several IP addresses, one for each network interface. Even if it isn't connected to anything, it'll have the localhost IP address. It might also have AirPort, Ethernet, PPP, or VPN IP addresses. If you want a list of all the addresses the computer has, use BSD functions. I think "getnetent()" will do the trick. -- DustinVoss

----

Your network addresses sans error checking...

    
NSEnumerator *addresses = [[[NSHost currentHost] addresses] objectEnumerator];
NSString *address;
while (address = [addresses nextObject])
	NSLog(@"%@", address);


It is easy to filter out local network addresses as these fall into well documented ranges. After filtering 127.0.0.1 (localhost) and the local net ranges, you may end up with nothing. Getting at a *external* network address may be a bit harder (if not impossible without actually connecting to something that tells you) as the local machine may not know it's *external* address(es) if it's behind a proxy or router/NAT. It may also have a different external address depending on the port it is communicating on (via. proxies). To make matters worse, the local machine or any of the proxies/servers could be multi-homed and have many external addresses.

Good Luck

----

There's some more information and examples on this thread: http://cocoa.mamasam.com/COCOADEV/2001/11/2/18253.php

----

Or try http://www.mycurrentip.com

Just a note on thread linked directly above, both of the code samples (I believe) completely ignore IPv6; which is not good network coding practice. The Foundation method does not. The Foundation method also does not assume the user has a *IP* address in the first place; may seem strange, but in the networking world, it can happen and will. AF_INET is not the only and absolute option nor was sockets coded as such.

----

If you want to get the WAN of the computer, you can run this Perl script throught NSTask:
    
#!/usr/bin/perl

while ($line = <STDIN>)
{
	if ($line =~ /<h1>Your IP  Is (.*)<\/h1>/igs)
	{
		print $1 . "\n";
	}
}
And call it like so from the Terminal (assuming your Perl file is named ip.pl and has correct permissions):
    curl -s 'http://www.whatismyip.com/' | ./ip.pl

----

I shouldn't have to point this out, but making your program parse data from an HTML page on a server that you don't own is a bad idea.  Whoever runs whatismyip.com might change their HTML in the future and all of a sudden your program will not work.

----

Using http://www.whatismyip.org/ is probably the most easy as it simply outputs the text containing the ip, no fancy stuff to make is a formatted webpage.

