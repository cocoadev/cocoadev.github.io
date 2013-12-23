---
layout: page
title: NSDistributedNotificationsInLaunchAgent
---

Hey

I have created a launch agent and have been successfully sending out NSDistributedNotifications to applications. However, I have trouble receiving any distributed Notifications inside the launch agent and I was wondering if I am doing something wrong with the run loop. My basic setup is as follows:


    
@interface aObject {}
-(void)anAction:(NSNotification*)aNotification;
@end

@implementation aObject
-(void)anAction:(NSNotification*)aNotification {
    fprintf(stdout, "Notification Recieved");
}
@end

int main () {
    // Initialization stuff for Launchd

   id myAction = aObject alloc] init];
	[[[[NSDistributedNotificationCenter defaultCenter] addObserver:myAction
		 selector:@selector(anAction:)
		name:@"AppSomeRandomSignal" object:Nil];
   while(running) {
         // do stuff
         // send distributed notification if needed
         usleep(for a minute);
   }
   // clean up and free memory
}



I have tried various combinations of receiving distributed notifications with no success including throwing the registration into the init of anObject. I am thinking it has something to do with my run loop going to sleep? Any help.

So in short I can send NSDistributedNotifications from a launch agent to an App and receive them fine, but I cannot do the opposite.

Thanks

