---
layout: page
title: UIProgressHUD
---



Part of the iPhone General/UIKit framework. Defined in <General/UIKit/General/UIProgressHUD.h>

    
- (void)showProgressHUD:(General/NSString *)label withWindow:(General/UIWindow *)w withView:(General/UIView *)v withRect:(struct General/CGRect)rect
{
        progress = General/[[UIProgressHUD alloc] initWithWindow: w];
        [progress setText: label];
        [progress drawRect: rect];
        [progress show: YES];

        [v addSubview:progress];
}

- (void)hideProgressHUD
{
        [progress show: NO];
        [progress removeFromSuperview];
}


----
You can also use General/MBProgressHUD as an alternative if you want to avoid Apple's private API. http://www.bukovinski.com/2009/04/08/mbprogresshud-for-iphone/
