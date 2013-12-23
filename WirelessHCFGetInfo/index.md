---
layout: page
title: WirelessHCFGetInfo
---



I am trying to use WirelessHCR_GetInfo() from the General/AppleEightOhTwoEleven header to get the channel of the currently joined wireless network. I have the following code:
    
int channel;
WirelessHCF_GetInfo(wdev, 0xFDC1, sizeof(channel), channel);

That isn't correct though. If I do printf("Channel: %i", channel); I get a channel of 0 which is just incorrect. So, if you know how to use this function I would appreciate a short example.

*I have no idea what this does, but if this is a normal C function you will need to pass the address of     channel to     WirelessHCF_GetInfo, like so: --General/JediKnil*
    
WirelessHCF_GetInfo(wdev, 0xFDC1, sizeof(channel), &channel);



Thanks. Also, the channel variable needs to be defined as a char and not an int.
