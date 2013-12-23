---
layout: page
title: XLog
---

General/XLog is an alternative to General/NSLog if you think General/NSLog's header is wide or you would like to log the elapsed time from a previous log.

**General/XLog.h**
    
extern void General/XLog(General/NSString *format, ...);
extern void General/XFTimeLog(General/CFAbsoluteTime *time, General/NSString *format, ...);


**General/XLog.m**
    
void _XLog(General/CFAbsoluteTime *lastTime, General/NSString *format, va_list argList) {

	General/CFAbsoluteTime time = General/CFAbsoluteTimeGetCurrent();
	static unsigned logcount = 0;
	if (logcount++ % 100 == 0) General/NSLog(@"logcount: %i", logcount);
	static General/CFTimeZoneRef zone = nil;
	if (!zone) zone = General/CFTimeZoneCopyDefault();
	
	General/CFGregorianDate date = General/CFAbsoluteTimeGetGregorianDate(time, zone);
	if (lastTime) {
		double elapsed_time = time - *lastTime;
		unsigned total_sec = elapsed_time;
		double fraction = elapsed_time - (double)total_sec;
		unsigned milli = fraction * 1000.0f;
		unsigned micro = fraction * 1000000.0f;
		micro %= 1000;
		// log elapsed time [sec.ms_us|pid]
		printf("[%03i.%03i_%03i|%i] ", total_sec, milli, micro, getpid());
	} else {
		unsigned sec = date.second;
		double fraction = date.second - (double)sec;
		unsigned milli = fraction * 1000.0f;
		// log standard time [hours:min:sec.ms|pid]
		printf("[%02i:%02i:%02i.%03i|%i] ", date.hour, date.minute, sec, milli, getpid());
	}

	General/CFStringRef log = General/CFStringCreateWithFormatAndArguments(NULL, NULL, (General/CFStringRef)format, argList);
	char *ptr = (char *)General/CFStringGetCStringPtr(log, kCFStringEncodingUTF8);
	if (ptr) 	
		printf("%s\n", ptr);
	else {
		unsigned buflen = General/CFStringGetLength(log) * 4;
		ptr = malloc(buflen);
		if (General/CFStringGetCString(log, ptr, buflen, kCFStringEncodingUTF8));
			printf("%s\n", ptr);
		free(ptr);
	}
	General/CFRelease(log);


}

void General/XLog(General/NSString *format, ...) {

	va_list argList;
	va_start(argList, format);
	_XLog(nil, format, argList);
	va_end(argList);

}

void General/XFTimeLog(General/CFAbsoluteTime *time, General/NSString *format, ...) {
	
	va_list argList;
	va_start(argList, format);
	_XLog(time, format, argList);
	va_end(argList);

	if (time) *time = General/CFAbsoluteTimeGetCurrent();

}



So something like this 
    
static void test(void) {

	General/XLog(@"test");

	General/CFAbsoluteTime time = General/CFAbsoluteTimeGetCurrent();
	int i;
	for (i = 0; i < 5; i++) {
		sleep(1);
		General/XFTimeLog(&time, @"loop[%i]", i);
	}

}


will log this

    
2005-11-26 15:45:32.264 Test[6783] logcount: 1
[15:47:01.818|6783] test
[001.000_082|6783] loop[0]
[000.999_974|6783] loop[1]
[000.999_972|6783] loop[2]
[000.999_974|6783] loop[3]
[000.999_972|6783] loop[4]


--zootbobbalu
