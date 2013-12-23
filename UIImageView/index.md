---
layout: page
title: UIImageView
---





<code>

//create background view
	
General/UIImageView *background = General/[[[UIImageView alloc] initWithFrame:General/CGRectMake(0.0f, 0.0f, 320.0f, 480.0f)] autorelease];

[background setImage:General/[UIImage imageAtPath:General/[[NSBundle mainBundle] 
             pathForResource:@"myGreatImage"
             ofType:@"png" 
             inDirectory:@"/"]]];

@property (nonatomic, retain) image <- image is already retained!

</code>
