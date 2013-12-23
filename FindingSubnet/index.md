---
layout: page
title: FindingSubnet
---



Hey,


As an aside to my post for MachineSpecificInformation, I need to find the subnet for a computer system.  I am almost completely lost on the IOKit, so if I have to use the IOKit, I will REALLY need some sample code.  Can anyone help with this?


ChrisGiddings


President, Ripple

chris.giddings **at** ripplesw.net

----

It looks like I have to mess with the System Configuration Framework in order to obtain this.  I am also not very familiar on this concept.  Does anyone know if the wrapper written by Malte will do this properly or not?


http://www.oops.se/~malte/software.html


Chris Giddings

President, Ripple


chris.giddings **at** ripplesw.net Dffs

*Chris, I really don't think you want to put your email address up raw here - I'm getting rid of the @ sign again.  No one will email you anyway, they will reply here so that the information can benefit everyone.*

Some of us have e-mail addresses that are so public and have been used for so long that there's no point in spamproofing them, because all the spammers already have them. Changing it is pointless anyway, since all you have to do is click the History button. -- MikeAsh (mike@mikeash.com)

----

Ok,

Here is some code which has been donated to me.  Unfortunately I do not know enough to read through and know which part of this is grabbing the sub-net. Can anyone point this out to me?  I need to dump the sub-net into a text field.  I was also suggested that the same code would solve my problem with IPv6 showing up instead of IPv4, and that somehow this code would allow me to always ensure that IPv4 would be pulled.  If anyone could help here, that'd be GREAT.  Code suggestions help as well.  Thanks.


    

struct ifaddrs *ifap, *ifp; 
     char ip[NI_MAXHOST], nm[NI_MAXHOST]; 
     
     if(getifaddrs(&ifap) < 0) 
     perror("getifaddrs"); 
     
     for(ifp = ifap; ifp; ifp = ifp->ifa_next) 
     { 
	 /* Ignore everything but IPv4 & IPv6*/ 
	 if(ifp->ifa_addr->sa_family != AF_INET && ifp->ifa_addr->sa_family != 
	    AF_INET6)
	     continue; 
	 
	 /* Ignore interfaces marked down. */ 
	 if(!(ifp->ifa_flags & IFF_UP)) 
	     continue; 
	 
	 /* Ignore interfaces without netmask. */
	 if(!ifp->ifa_netmask) 
	     continue; 
	 
	 if(getnameinfo(ifp->ifa_addr, ifp->ifa_addr->sa_len, ip, sizeof(ip), 
			NULL, 0, NI_NUMERICHOST) != 0) 
	     perror("getnameinfo"); 
	 
	 if(getnameinfo(ifp->ifa_netmask, ifp->ifa_netmask->sa_len, nm, 
			sizeof(nm), NULL, 0, NI_NUMERICHOST) != 0) 
	     perror("getnameinfo"); 
	  
	 printf("%s %s %s\n", ifp->ifa_name, ip, nm);
     }



ChrisGiddings

President, Ripple


chris.giddings **at** ripplesw.net

