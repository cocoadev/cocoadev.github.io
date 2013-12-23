---
layout: page
title: NSProgressIndicator
---

NSProgressIndicator causes an application to display a progress indicator to show that a lengthy task is under way. Some progress indicators are indeterminate and do nothing more than spin to show that the application is busy. Others are determinate and show the percentage of the task that has been completed.
  
See: http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSProgressIndicator_Class/index.html

----

In interface builder one can disable *Display When Stopped*, but for me it (correctly) results in an initially hidden progress bar, which appear the first time the value is changed, **but** when it stops again (the value going back to min or max), it will not be hidden.

Is this a known bug? I am using the 10.2 Nib format.

----
Does anyone know of ways to change the appearance of the progress indicator bar in any way, other than the determinate/indeterminate difference?  I'd like to change the color, even though I know I'm probably breaking some Apple UI rule by doing so.  I could probably knock together a custom indicator but if there are any features available on the existing ones I'd like to try that first --StevenNewton

*use <code>-[NSProgressIndicator setControlTint:]</code>.* --boredzo

Am I missing something? As far as I can tell, that only lets you pick between default, blue, graphite, or clear --  I'm guessing "clear" means the transparent color of an unselected control. Regardless, that doesn't help somebody trying to make it, say, fuscha.

--ShamylZakariya

Also, setControlTint doesn't appear to change the tint at all. The property is changed (verified by controlTint:) but offers no visible change.
-- Drarok

----

From Above:

--------------------------------

In interface builder one can disable *Display When Stopped*, but for me it (correctly) results in an initially hidden progress bar, which appear the first time the value is changed, **but** when it stops again (the value going back to min or max), it will not be hidden.

Is this a known bug? I am using the 10.2 Nib format. 

--------------------------------

Start you progress bar with like this.

[progressBar startAnimation: self];

When you are done you need to do this...

[progressBar stopAnimation: self];


Small timer sample
    

NSTimer *timer;

if(!timer)
{
     timer = [[NSTimer scheduledTimerWithTimeInterval:0.05 
                     target:self selector:@selector(checkThem:) 
                     userInfo:nil repeats:YES] retain];
	[progressBar startAnimation: self];
}

-(void)checkThem:(NSTimer *)aTimer
{
	count++;
	if(count > 100)
	{
		count = 0;
		[timer invalidate];
		[timer release];
		timer = NULL;
		[progressBar setDoubleValue:0.0];
		[progressBar stopAnimation: self];
	}
	else
	{
		[progressBar setDoubleValue:(100.0 * count) / 100;
	}
}
 
-Jason O
----

see also NSProgressIndicatorBeStill


----

I have a question about this, too.  Hope you don't mind.  Basically, I am building an application where you click a button and it loads a webpage within the application (sort of similar to the Build You Own Simple Web Browser).  I want the indeterminate progress gear to spin until the page is loaded.  However, currently it only spins when I click the button to load the page and then disappears.  My code looks like this so far:

    
- (IBAction)goToURL:(id)sender;
{	
	[indeterminateProgress startAnimation: self];

	NSString *apple = [NSString stringWithString:@"http://www.apple.com"];
		webView mainFrame] loadRequest:[[[NSURLRequest requestWithURL:[NSURL URLWithString:apple]]];

	[indeterminateProgress stopAnimation: self];
}


So my question is how do I get the progress gear to spin when the button is pressed and then stop when the webpage has fully loaded?  Thanks for the help in advance!  Still trying to learn all of this :-) 
-Ryan

----

Check out the Mini browser example on your system at: /Developer/Examples/WebKit/MiniBrowser/. And look at the documentation at:


* - (void)webView:(WebView *)sender didStartProvisionalLoadForFrame:(WebFrame *)frame
* - (void)webView:(WebView *)sender didFinishLoadForFrame:(WebFrame *)frame


-- Jacob

----

Not quite what I was looking for, but it turns out it'll work better in the long run.  Thanks, Jacob!  Appreciate the help! -Ryan

----

Ryan - 

To show a spinning progress indicator in the WebKit:MiniBrowser example:
1. Add this line to the MyDocument.h
    
IBOutlet NSProgressIndicator *progressIndicator;

2. Save the .h file.
3. Open the document.nib and drag the .h file into it.
4. Add an NSProgressIndicator to the MiniBrowser document.nib and save the nib.
5. Connect your new outlet to the NSProgressIndicator (Control drag from the first responder to your NSProgressIndicator).
6. Add the following line to didStartProvisionalLoadForFrame: in MyDocument.m.
    
[progressIndicator startAnimation: self];

7. Add the following line to didFinishLoadForFrame in MyDocument.m. 
    
[progressIndicator stopAnimation: self];


-- Russ

----

Hey, I'm trying to use an indeterminate NSProgressIndicator in my program, but it seems to not be working. I hooked up the action to run when a button is clicked and the action then tells the progress indicator to start, runs some code to change an large RSS feed into a dictionary and populate it into a table, then tells the indicator to stop. What it is doing instead is the button stays down for the entire time the function is running and the indicator never starts. I tried setting usesThreadedAnimation to Yes, but that didn't do anything, I tried using one of these autorelease pool things (I probably used it incorrectly, I defined the pool after the animation started and released it before it should end) and that didn't work. So is there something I am doing wrong. I can post some code if I need too.

-- ACoolie

----
Are you animating the progress indicator in your code before processing the RSS feed?

-- cracker

----
I think so (I set it to be a spinny indeterminate indicator, so I figure all the code I need is start/stop animation?). There is:
    
[progress startAnimation:self];
system("..."); // Run an executable to make a plist out of an RSS file.
// Some code to move that plist into an array
// then loop through it with an enumerator and
// create a dictionary with the contents of each array
// and add that row to the table.
[progress stopAnimation:self];


----
First, don't call     system(), it's very difficult to make it work correctly in all situations. Use NSTask instead. Second, break up your processing so it doesn't block the main runloop. Break it into pieces you can execute with an NSTimer, or spin it off into another thread.

----
Have you tried:
    
[progressBar setUsesThreadedAnimation:YES];


----

Hello

I have a very strange problem. I am using indeterminate progressBar when I am using spining style than it runs well. But as i am switching to Bar style it stops running. Please anyone help me out .. what is going wrong ..

Thank you.
-Archita

----
Please don't post your question to multiple places simultaneously. It's rude and wastes people's time because people in one place can't see answers posted in the other place.

----
I am attempting to put the spinning NSProgressIndicator on a dark background and the control is drawing a light background, so it looks bad.  Is there some way to get the control to not draw its background or at least specify its background color?

--Guy Umbright

----
This is almost certainly a problem with the view that the NSProgressIndicator is in, not the NSProgressIndicator itself. See DrawRect.

----
also, if you do not want it to be indeterminate, go into interface builder, and deselect "indeterminate". i had problems with that. just adding for the sake of others.

