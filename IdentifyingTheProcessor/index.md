---
layout: page
title: IdentifyingTheProcessor
---

How can I identify the processor that is running the application so I could execute a fragment of code only if it's running on a G4?
    --Angel Puerto

Do you want to know if it is Altivec enabled?

----

    
#include <sys/sysctl.h>

boolean_t IsG4()
{
	host_basic_info_data_t hostInfo;
	mach_msg_type_number_t infoCount;
	
	infoCount = HOST_BASIC_INFO_COUNT;
	kern_return_t ret = host_info(mach_host_self(), HOST_BASIC_INFO, (host_info_t)&hostInfo, &infoCount);
	
	return (KERN_SUCCESS == ret && (hostInfo.cpu_type == CPU_TYPE_POWERPC) && (hostInfo.cpu_subtype == CPU_SUBTYPE_POWERPC_7400 || hostInfo.cpu_subtype == CPU_SUBTYPE_POWERPC_7450));
}                                       

boolean_t IsAltiVecAvailable()
{
	int name[2] = { CTL_HW, HW_VECTORUNIT };
	int hasVectorUnit = 0;
	
	size_t length = sizeof(hasVectorUnit);
	int err = sysctl(name, 2, &hasVectorUnit, &length, NULL, 0);
	
	return (0 == err && 0 != hasVectorUnit);
}


----

Or, you could simply send gestaltNativeCPUType to Gestalt(). It will return gestaltCPUG3, gestaltCPUG4, gestaltCPUG5, yada yada yada...
----
ADC recommends the sysctl method for mach-o executables, Gestalt for Classic or CFM Carbon.

That makes sense. The sysctl interface should be more stable than Gestalt. Only depend on it if you are already.

type sysctl -A in the terminal to see what else you can find out.

