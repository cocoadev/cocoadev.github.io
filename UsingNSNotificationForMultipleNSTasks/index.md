---
layout: page
title: UsingNSNotificationForMultipleNSTasks
---

Hi,
I'm a real inexperienced programmer so please bare with me.  I'm trying to get some information from a file that is written after 5 different General/NSTasks end.  Now if this was only one General/NSTask I have no problem but with the code I'm using right now:

General/[[NSNotificationCenter defaultCenter] addObserver:self 
            selector:@selector(finishedDownload:) 
            name:General/NSTaskDidTerminateNotification 
            object:nil];
			
In the -(id)init of my file, When 4 different General/NSTasks call it simulataneouly it crashes the application. Does anybody no how to work around this?  Or is there any other way to know when General/NSTasks end?

Thanks

----

What does your     finishedDownload: look like?

If the task order is determinate, and you have a pointer to your last General/NSTask, you can pass that to General/NSNotificationCenter for     object: and you'll only receive the notification for that task.

finishedDownload: looks like this:
    
- (General/NSString *)finishedDownload:(General/NSNotification *)aNotification {
	General/NSString *lastUpdate;
	home2=General/NSHomeDirectory();
	
	General/NSString *updateString=General/[NSString stringWithContentsOfFile:home2];
		General/NSRange range1;

	if (totalMarker1 != nil) {
home2=[home2 stringByAppendingString:@"/Library/Xplanet/markers/updatelabel"];
			lastUpdate=@"Last Storm Update: ";
			range1=[updateString rangeOfString:@"color="];
			updateString=[updateString substringFromIndex:range1.location + range1.length];
			range1=[updateString rangeOfString:@"image="];
			
			
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		General/NSString *stormString = [updateString substringToIndex:range1.location-1];
					if ([stormString isEqualToString:@"Green"] == TRUE)
				lastUpdate=[lastUpdate stringByAppendingString:@"Successful"];
			else
				lastUpdate=[lastUpdate stringByAppendingString:@"Failed"];
			
			[lastStormUpdateField setStringValue:lastUpdate];
			[stormProgress stopAnimation:nil];
			[totalMarker1 release];
	}

else if (totalMarker2 != nil) {
	updateString=General/[NSString stringWithContentsOfFile:home2];
	lastUpdate=@"Last Satellite Update: ";
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		General/NSString *satelliteString = [updateString substringToIndex:range1.location-1];
			if ([satelliteString isEqualToString:@"Green"] == TRUE)
				lastUpdate=[lastUpdate stringByAppendingString:@"Successful"];
			else
				lastUpdate=[lastUpdate stringByAppendingString:@"Failed"];
				
				[lastSatelliteUpdateField setStringValue:lastUpdate];
	[satelliteProgress stopAnimation:nil];
	[totalMarker2 release];
			
}
else if (totalMarker3 != nil) {
	updateString=General/[NSString stringWithContentsOfFile:home2];
		lastUpdate=@"Last Volcano Update: ";
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		
		
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		General/NSString *volcanoString = [updateString substringToIndex:range1.location-1];
			if ([volcanoString isEqualToString:@"Green"] == TRUE)
				lastUpdate=[lastUpdate stringByAppendingString:@"Successful"];
			else
				lastUpdate=[lastUpdate stringByAppendingString:@"Failed"];
				//x++;
			
			[lastVolcanoUpdateField setStringValue:lastUpdate];
	[volcanoProgress stopAnimation:nil];
    [totalMarker3 release];
}
else if (totalMarker4 != nil) {
	updateString=General/[NSString stringWithContentsOfFile:home2];
lastUpdate=@"Last Earthquake Update: ";
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		General/NSString *quakeString = [updateString substringToIndex:range1.location-1];
			if ([quakeString isEqualToString:@"Green"] == TRUE)
				lastUpdate=[lastUpdate stringByAppendingString:@"Successful"];
			else
				lastUpdate=[lastUpdate stringByAppendingString:@"Failed"];
			[lastQuakeUpdateField setStringValue:lastUpdate];
			[quakeProgress stopAnimation:nil];
			[totalMarker4 release];
}
else if (totalMarker5 != nil) {
	updateString=General/[NSString stringWithContentsOfFile:home2];
		lastUpdate=@"Last Cloud Update: ";
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		
		range1=[updateString rangeOfString:@"color="];
        updateString=[updateString substringFromIndex:range1.location + range1.length];
		range1=[updateString rangeOfString:@"image="];
		General/NSString *cloudString = [updateString substringToIndex:range1.location-1];
			if ([cloudString isEqualToString:@"Green"] == TRUE)
				lastUpdate=[lastUpdate stringByAppendingString:@"Successful"];
			
			else
				lastUpdate=[lastUpdate stringByAppendingString:@"Failed"];
			[lastCloudUpdateField setStringValue:lastUpdate];
			[cloudsProgress stopAnimation:nil];
			[totalMarker5 release];
			
}

This way I determine the different tasks are to see which one is nil.  This is very inefficent way of doing it.  I'd like some more details about this determinate thing you are talking about.  Exactly what do I have to do to the tasks?

----

I think what he means is that you could put a specific task as an object in the addObserver method. If you know one of the task always finishes last, then you could have that task being this object. If you don't know which one finishes last, you could have a separate selector called for each of the task, so you just don't have the if-else (but does not necessarily make things better or more efficient, I guess).

Also, I don't think the notifications are sent "simultaneously", if by that you mean separate threads. But I am not really sure, actually. Maybe somebody could answer that... thanks!

----

Assuming the     totalMarkers are General/NSTask objects, try changing the     if (totalMarker(X) != nil) tests to     if ([totalMarker(X) isRunning]) or     if ([totalMarker(X) terminationStatus] != 0). See docs at [http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/General/NSTask.html]

It's hard to tell what you're trying to do here - when one task finishes, you test all the tasks, then if they *aren't* nil (still running, presumably),  you update some UI state, then release the task.

Also, don't forget that you can simply retrieve the object that posted the notification by doing     [aNotification object] which may save you some testing.
