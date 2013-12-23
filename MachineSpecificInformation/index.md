---
layout: page
title: MachineSpecificInformation
---

I have been tasked with producing a tool for an IT department which will collect all of our computer systems' Serial Numbers, MAC (ethernet) address IDs (the primary/active ones), the computer's subnet, it's IP address, and some other information.

I am currently using: 

    
[ipAddress setStringValue:[[NSHost currentHost] address]]; 


where ipAddress is a text field spitting the current IP from the primary connection into the field.  However, this will return an IPv6 string in Panther, unless IPv6 has been explicitly turned off.  Does anyone know how to ensure an IPv4 address is read?

I am using this following code in order to obtain the MAC (ethernet) address ID.  I'm having trouble dumping it into a text field as opposed to dropping it to an NSLog.  Help with this?

    
    InetInterfaceInfo myInetInterfaceInfo;
    int i, adaptorInterface;
    NSString *eNetAddress;
    NSLog(@"Ethernet addresses: ");
    adaptorInterface=0;

    do
	{
	    OTInetGetInterfaceInfo(&myInetInterfaceInfo, adaptorInterface);
	    if (myInetInterfaceInfo.fHWAddrLen > 0) 
		{
		    for(i=0; i<myInetInterfaceInfo.fHWAddrLen; i++)
		    {
			eNetAddress == (@"%02x ",myInetInterfaceInfo.fHWAddr[i]);
			[macAddress setStringValue:eNetAddress];
		    }
		    //NSLog(@"\n");
		}
		adaptorInterface++;
	}
    while (myInetInterfaceInfo.fHWAddrLen > 0);


I have used this code (taken from a mail list archive) in order to find the computer's Serial Number.  I know some machines do not have them, but all of the ones in our organization do. However, this code breaks in two spots:

    
io_registry_entry_t entry;


and:

    
kr = IOMasterPort(MACH_PORT_NULL, &masterPort);


Here's the full code:

    
	NSString *result2 = @"";
	mach_port_t masterPort;
	kern_return_t kr = noErr;
	io_registry_entry_t entry;� � �
	CFDataRef propData;
	CFTypeRef prop;
	CFTypeID propID;
	UInt8 *data;
	unsigned int i2, bufSize;
	char *s, *t;
	char firstPart[64], secondPart[64];
	kr = IOMasterPort(MACH_PORT_NULL, &masterPort);� �
	if (kr == noErr)
	    {
		entry = IORegistryGetRootEntry(masterPort);
	    if (entry != MACH_PORT_NULL)
		{
		    prop = IORegistryEntrySearchCFProperty(entry, kIODeviceTreePlane, CFSTR("serial-number"), NULL, kIORegistryIterateRecursively);
		    propID = CFGetTypeID(prop);
		    if (propID == 5)
		    {
			propData = (CFDataRef)prop;
			bufSize = CFDataGetLength(propData);
			if (bufSize > 0)
			{
				    data = CFDataGetBytePtr(propData);
				    if (data)
				    {
					    i2 = 0;
					    s = data;
					    t = firstPart;
					    while (i < bufSize)
					    {
						    i2++;
						    if (*s != '\0')
						    {
							    *t++ = *s++;
						    } 
						    else 
						    {
							    break;
						    }
					    }
					    *t = '\0';
					    while ((i < bufSize) && (*s == '\0')) 
					    {
						    i2++;
						    s++;
					    }
					    t = secondPart;
					    while (i < bufSize)
					    {
						i2++;
						if (*s != '\0')
						    {
							*t++ = *s++;
						    }
						    else
						    {
							    break;
						    }
					    }
						*t = '\0';
						result2 = [NSString stringWithFormat:@"%s%s", secondPart, firstPart];
					    }
				    }
			    }
		    }
		    mach_port_deallocate(mach_task_self(), masterPort);
	    }
	return(result2);


Both breaks are very early on and I have been unable to determine what exactly is wrong.  Hopefully just because I am not as thoroughly versed in the IOKit as I wish I was.

Any help on these, and helping me to find the subnet would be greatly appreciated.  Thanks in advance.

ChrisGiddings

----

I posted some code a while ago, that I got from somebody else too, and I have since written a little tool called 'minime' that I use on my Xgrid cluster to collect some info, including S/N. Look at HowToGetHardwareAndNetworkInfo, and/or send me an email for the minime tool :-)
--CharlesParnot

