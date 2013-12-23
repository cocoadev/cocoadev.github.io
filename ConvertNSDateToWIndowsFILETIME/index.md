---
layout: page
title: ConvertNSDateToWIndowsFILETIME
---

I need to be able to convert from an NSDate to a Windows FILETIME struct. I get the timeintervalsince1970, and then run it through this function, which is supposed to work (got it from the MS website).

    
FILETIME UnixTimeToFILETIME(time_t t)
{
	
	FILETIME retf;
	long long int ll;
	ll = ((long long)t * 1000000LL);
	ll += 116444736000000000LL;
	retf.dwHighDateTime = ll >> 32;
	
	retf.dwLowDateTime = ll & 0x00000000FFFFFFFF;
	return retf;
}


The reverse function, which gets a unix time from FILETIME, is this: (and works fine)
    
time_t FILETIMEToUnixTime(FILETIME filetime)
{
	long long int t = filetime.dwHighDateTime;
    t <<= 32;
    t += (unsigned long)filetime.dwLowDateTime;
    t -= 116444736000000000LL;

	return t / 10000000;


}


I can't seem to figure out the problem, unless I'm not getting a valid NSDate from the NSFileManager. (Which I am)

----

Multiply by 10000000, not 1000000?

The version that I found on Microsoft's support site does this.

    
 void UnixTimeToFileTime(time_t t, LPFILETIME pft)
 {
   // Note that LONGLONG is a 64-bit value
   LONGLONG ll;

   ll = Int32x32To64(t, 10000000) + 116444736000000000;
   pft->dwLowDateTime = (DWORD)ll;
   pft->dwHighDateTime = ll >> 32;
 }
 

Also take care to adjust the byte ordering if you plan to read this structure on/from an x86.

