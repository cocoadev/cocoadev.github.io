---
layout: page
title: MBProgressHUD
---

MBProgressHUD is replacement for the undocumented UIProgressHUD UIKit class with some additional features. 
The class displays s translucent HUD with a progress indicator and some optional labels while work is being done in a background thread. 
The HUD can show both determinate and indeterminate progress. 

More info and download: http://github.com/matej/MBProgressHUD and http://www.bukovinski.com

    

#import <UIKit/UIKit.h>
#import "MBProgressHUD.h"

@interface HudDemoViewController : UIViewController <MBProgressHUDDelegate> {
	MBProgressHUD *HUD;
}

- (IBAction) showWithLabel:(id)sender;

- (void) myTask;

@end



    

#import "HudDemoViewController.h"

@implementation HudDemoViewController

- (IBAction) showWithLabel:(id)sender {

	// Should be initialized with the windows frame so the HUD disables all user input by covering the entire screen
	HUD = [[MBProgressHUD alloc] initWithWindow:[UIApplication sharedApplication].keyWindow];

	// Add HUD to screen
	[self.view.window addSubview:HUD];

	// Register for HUD callbacks so we can remove it from the window at the right time
	HUD.delegate = self;

	HUD.labelText = @"Loading";

	// Show the HUD while the provided method executes in a new thread
	[HUD showWhileExecuting:@selector(myTask) onTarget:self withObject:nil animated:YES];

}

- (void) myTask {
	// Do something useful in here instead of sleeping ...
	sleep(3);
}

- (void)hudWasHidden {
	// Remove HUD from screen when the HUD was hidden
	[HUD removeFromSuperview];
	[HUD release];
}

@end


