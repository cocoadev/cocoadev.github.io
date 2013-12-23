---
layout: page
title: MicroUnixTimeAsString
---



Hi there, I've been trying to figure out how to get the current time since 1970 in microseconds, as a NSString. Any help for this noob would be appreciated.

Thanks,
Alexandre

----
I'm going to have to say RTM in general, but basically you should look to NSDate's     timeIntervalSince1970, NSTimeInterval, and NSString's     initWithFormat:. --JediKnil

----

A note: NSTimeInterval values measure seconds. Microseconds (which due to the nature of floating point values will eventually be unrepresentable) can be got from a time interval by multiplying by one million, e.g. with the following macro:

    
#define MicrosecondsFromTimeInterval(TI) ((TI) * 1000000.0)


----
If you really need a high resolution timer look at gettimeofday.

    
cristi:~ diciu$ cat gettime.c 
#include <sys/time.h>

int main()
{
        struct timeval tp;

        while(1)
        {
                gettimeofday(&tp, NULL);

                printf("seconds %ld, microseconds %ld\n", tp.tv_sec, tp.tv_usec);
                usleep(100);
        }
}
cristi:~ diciu$ gcc gettime.c 
gettime.c: In function 'main':
gettime.c:11: warning: incompatible implicit declaration of built-in function 'printf'
cristi:~ diciu$ ./a.out | head
seconds 1173355247, microseconds 222851
seconds 1173355247, microseconds 223096
seconds 1173355247, microseconds 223215
seconds 1173355247, microseconds 223333
seconds 1173355247, microseconds 223461
seconds 1173355247, microseconds 223579
seconds 1173355247, microseconds 223696
seconds 1173355247, microseconds 223825
seconds 1173355247, microseconds 223942
seconds 1173355247, microseconds 224059


hth, CristianDraghici


----

Hey guys, I ended up writing this function to do it...

    
- (NSString *)getCurrentTime
{
NSTimeInterval todayinterval = [[NSDate date] timeIntervalSince1970];
NSString *aString = [NSString stringWithFormat:@"%f", todayinterval];
return aString;
}


Alexandre

