---
layout: page
title: SlowAndFastDoubleClick
---




    
- (IBAction) click:(id)sender
{
	fastClick++;
	slowClick++;
	int clicked = [localFilesView clickedRow];
	
	NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
  float doubleClickTime=defaults objectForKey:@"com.apple.mouse.doubleClickThreshold"] 
                                         floatValue];
	
	[[[NSTimer scheduledTimerWithTimeInterval:doubleClickTime
       target:self selector:@selector(fastClickReset:) userInfo:nil repeats:NO];
	[NSTimer scheduledTimerWithTimeInterval:doubleClickTime*10
       target:self selector:@selector(slowClickReset:) userInfo:nil repeats:NO];
	
	if(clicked == lastSelectedRow)
	{
		if(fastClick > 1)
		{
			//Open Cell
			fastClick = 0;
		}
		else if(slowClick > 1)
		{
			//Edit Cell
			slowClick = 0;
		}
	}
	else
	{
		fastClick = 0;
		slowClick = 0;
	}
	
	lastSelectedRow = [localFilesView clickedRow];
}



What is wrong with this code, i cant make it work 100%.
I'm trying to make this work like in transmit or itunes where slow double click edits, an fast opens.

Tnx

----

I've solved it with this code

    
- (IBAction) click:(id)sender
{
int clicked = [localFilesView clickedRow];
	
	NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
float doubleClickTime = defaults objectForKey:@"com.apple.mouse.doubleClickThreshold"] floatValue];
	
	BOOL doubleClick     = ([[[localFilesView window] currentEvent] clickCount] == 2);
	
	
	if(clicked == lastSelectedRow)
	{
		if(doubleClick)
		{
			[localFiles setDir:[[[NSString stringWithFormat:@"%@%@/",
                             [localFiles getDir], [localFiles getContent] objectAtIndex:clicked] fileName];
			[localFilesView reloadData];
		}
		else
		{
			NSArray *event = [NSArray arrayWithObjects:localFilesView,
                                              [NSNumber numberWithInt:[localFilesView clickedColumn]],
                                              [NSNumber numberWithInt:clicked], nil];
			[self performSelector:@selector(editTableRow:) withObject:event afterDelay:0.2];
		}
	}
	
	lastSelectedRow = [localFilesView clickedRow];
}


----

Don't get the double click time from defaults, use Carbon's GetDblTime() instead.

    float dblClickTime = (GetDblTime() / 60.0);

