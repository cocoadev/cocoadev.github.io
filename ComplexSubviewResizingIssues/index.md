---
layout: page
title: ComplexSubviewResizingIssues
---

I have a default Cocoa Application project, I have cleaned my targets, when I compile, I get WRONG behavior, but when I Debug I get CORRECT behavior.
the behavior that is wrong is that I have two subViews, and my superview lays them out.
this is done by looping through the list of subviews, and lining them up vertically.
then My superview resizes itself to be just a hair taller than the combined height of its subViews.

when I run the app, the loop drops out Before it gets to the last subView, so My SuperView winds up Cropping out the last view!


BUT 

when I debug the app... Lo and Behold... it works as I designed it to work!  Not only Can I Not deploy an App that NEEDS to be in Debug mode in order to work properly, but I cannot even Test the problem!

And Get This! if I go into IB where I have my subviews laid out inside the superView, and just slightly Move them... the behavior Changes !   Sometimes it works!


has anybody had this problem?   What the heck is going on? 

heres a snippit of my update UI method.
its messy, because I have been trying for hours to get this to work...
    
- (IBAction)updateUI:(id)sender
{
	NSRect newFrame;
	int newMyHeight;
	NSEnumerator *childrenEnum;
	BTInfoSubBox	*theChildAtHand;
	int i;

	self superview] setNeedsDisplay:YES];


->	**newMyHeight = 10;**


	newFrame.origin.x = 4;
	newFrame.origin.y = [self frame].size.height - 10;
	newFrame.size.width = [self frame].size.width - 12;
	i = 0;

	//childrenEnum = [childViews objectEnumerator];  // used to use an enumerator, but I thought it was The problem, so I went back to a good ol "While loop"
	while (i < [childViews count])//(theChildAtHand = [childrenEnum nextObject])
	{
		newFrame.origin.y = newFrame.origin.y - ([[childViews objectAtIndex:i] frame].size.height);
		newFrame.size.height = [[childViews objectAtIndex:i] frame].size.height;
		[[childViews objectAtIndex:i] setFrame:newFrame];
		newMyHeight = newMyHeight + [[childViews objectAtIndex:i] frame].size.height;
		i ++;
	}
	newFrame.origin.x = [self frame].origin.x;
// this resets the origin, the hard way Because for some reason Inverted Y coords doesn't F'n Work.
	if(newMyHeight > [self frame].size.height)
	{
		newFrame.origin.y = [self frame].origin.y - (newMyHeight - [self frame].size.height);
	}
	else
	{
		newFrame.origin.y = [self frame].origin.y + ([self frame].size.height - newMyHeight);
	}
	newFrame.size.height = newMyHeight;
	newFrame.size.width = [self frame].size.width;
	[self setFrame:newFrame];
}


----

**This is an excruciating mess. Sorry. ;-) You're going to have to back up and explain from the beginning, very clearly, what you are trying to accomplish and try to be a bit more clear about the problem itself. Also, please *CLEAN UP* the code and arrange it how you think it should be. There's too much crap there to ask people to read through for free. Help us help you! ;-)**

Oh, and *trust* the enumerator. It won't do you wrong unless you do *it* wrong -- use it normally and you won't have any problems. On first guess, given your assertion that it sometimes works when you mess with it in IB ... it may be that you've got overlapping views? Strange stuff happens when this happens ... Make sure none of your views in your nib overlap!

----

at its simplest level, 
I am traversing an array of views.
doing something to each of them.

when I compile and run, whether i use an enumerator or  while( i < [array count]), the final object is skipped. 
but it is definitely in the array.
then when I debug, the last object is Not skipped.

none of my views overlap, and it isn't relevant to the discussion if they are. I am not traversing a mouse hit event, I am traversing an array of subviews. 
whether they overlap or not, they are entries in the array.

**Listen, you're asking us for help because you don't know what the problem is. If you knew, you wouldn't be asking -- it in fact is VERY relevant if you're just assuming the object in the array is being skipped. If it isn't then the view itself is just being positioned outside the viewable region of the superview. This happened to me. If you want to keep assuming, please don't ask for help; I've better things to do.  ... to clarify (I was rushed) - nobody here has any idea beyond what you're telling us. Since we all regularly use the Cocoa API (enumerators, et al) successfully, it's more than likely your code or your nib acting up. by making sure we rule things out, we're that much closer to the solution. If you don't want to even bother to consider what others are suggesting, don't waste our time asking.**

Strange stuff is happening, but I find that More and more... strange stuff is happening. Browsers don't respect delegate methods, autoresize is screwed up, and now I have a very simple array forgetting that it has members.  I am beginning to suspect [[ZeroLink or something lately added to Xcode that I just don't know that I should turn off.

*That stuff sounds like a general nib problem to me. Perhaps you should back up your old one and create a new one. This (funky nibs) is a fairly common affliction that only elbow grease (recreating your nib) tends to solve. This would also explain your views disappearing - if the scroll view and its document view aren't autosizing correctly, that could make it appear to have lost the last view.*

you say trust enumerators? I say trust no one.  Something is definitely fishy, I'm not in Denmark, and it ain't the fish.

btw: wiki is screwing up my line endings.  everything looks right until I hit save changes.

----

PLEASE stop making so many edits so quickly without checking to see if there's been a change -- this is the fourth time I've had to repost my response to you. Um ... did you try the IB suggestion? And what *exactly* are you doing with the views, just resizing / repositioning them? Are you sure the last view is being touched? Perhaps you should rebuild your method using the enumerator normally and have it call a "touchMe" method that NSLog's out the fact that "I'm child view 6 and I've been touched" (insert inappropriate Michael Jackson comments here). This would tell you right away if your assumption is true or it's just your positioning math (which may be putting the subview somewhere you can't see). I managed to create a routine that accomplishes the exact same goal inside of an hour (I'd never done anything like that before) and with a little finesse, it works perfectly fine. "It ain't the enumerator."

---- 

funny.
I myself have just had MY post erased.

I am positioning my views vertically. then sizing the superview so that it is just bigger than the combined subViews. and then redrawing the whole thing.

in IB I have a superView (NSBox subclass) two subViews (NSBox subclass)  the sub views are both already vertically arranged, and not overlapping.

I have painstakingly checked this in my IB doc AND the documentation numerous times, hence that being not relevant.

my SubViews? have an outlet to the superview, in their "awakeFromNib" method, they send a "addMe:self" message to the superView. the superView adds them to an array and then Updates its layout.

in the updateUI: method the superview, steps through the array of subViews, it resets the frame of each of them. making the width match the width of the super view making the origin line up with the left edge, and placing each subView in descending order vertically. leaving the height alone, but adding it to an integer that totals all of the heights of the subViews.

when done, I set the frame of the superView. making its height, match the total height of its subviews. this is the part that breaks.

in debug mode, it works exactly the way it should work. when running it, it Does indeed crop off one of the views, and right now it looks like it skips the First View, not the last. But this cannot be because of it overlapping, because they don't overlap.

heres my loop where i step through the subViews: newMyHeight is the int var that is giving me trouble. under debug it adds up, during a run it appears to replace its value or, skip the first object in the array.
    
i = 0; // childrenEnum = [childViews objectEnumerator];
while (i < [childViews count]) // (theChildAtHand = [childrenEnum nextObject])
{
  newFrame.origin.y = newFrame.origin.y - (childViews objectAtIndex:i] frame].size.height);
  newFrame.size.height = [[childViews objectAtIndex:i] frame].size.height;
  [[childViews objectAtIndex:i] setFrame:newFrame];
  newMyHeight = newMyHeight + [[childViews objectAtIndex:i] frame].size.height;
  i ++;
}

*Sorry for interfering with your code, but it was hard to read. Just remember to put it between \\%\\%BEGINCODE\\%\\% and \\%\\%ENDCODE\\%\\%. --[[JediKnil*

Do you set NewMyHeight to zero somewhere?

----
i start with NewMyHeight = 10;  its to offset from the rendered edges of the NSBox.
see the now out of date example at the top of the thread for an idea of where I do that in context.

----
I came at this from a different angle this morning.
I treated the behavior of the debugger as a bug in the OS and I started troubleshooting instead of bug-hunting.
the first thing I tried Identified the nature of the problem.

I logged in as a different user and recompiled my app, and then ran it and then debugged it.
lo and behold, similar results both times.

this indicates something is screwed up in my User account, likely the preferences associated with Xcode, but it "could' be something else. 

thanks for all your help 

----
**solution:**

the problem was caused by resizing my superview After placing all of the subviews.
so what to do to fix it, is resize from the top down.

*Or resize *before* reorganizing by precalculating the heights and vertical margins.*

there IS a bug in hard coding the "sproings" on subviews. for some reason, the whole mechanism is messed up to the point that you have to start out by telling the parent view to NOT autoResizeSubviews.  then it works, sort of.  apparently its not reliable and doesn't consistently override the "sproing" behavior that is set in IB.  Because that was what was happening...

my view would shuffle the subViews around, line them up perfectly, and then resize itself,
 autoresizing the subViews, which had almost random "sproing" settings on each one, due to the bug.
this made some of the subViews grow their height drastically and overlap each other Outside of the frame of the superview... causing wild behavior, without any visible evidence.

*Interesting how my app does the same thing with a view and subviews yet when all the auto-sizing is set properly, it works. ;-)*


----
everything works just fine if I set the "sproings" in IB, but that is inappropriate for my needs.  
I'm not making a static feature to an application, I'm making a configurable framework, that allows the developer to generate a hierarchy in IB, or add and remove subViews from Nibs,  or even generate subviews from scratch.  To support this, I have to be able to go back n forth between IB and programatically working with the subviews.  My subview needs to be available in a palette in IB,  I need to make sure stuff doesn't brake in the future because of something I didn't foresee, in short I have to make this thing as bulletproof as possible. I doubt we have the same needs and I doubt our applications do the same exact thing.  Any subtle differences could introduce problems. 

----

*Editor: This is really a rather toxic discussion for my tastes. But then, I don't hang out at /.
The OP's impenetrable narcissism prevent both him and us from learning very much at all.*

**Sorry to dredge this page up again, but I have to add my support to the Editor above - this is a prime example of how *NOT* to ask the Cocoa community (or any intelligent gathering of individuals) for help. I don't think narcissism is quite strong enough a word for the attitude and approach of the OP on this page. It just makes you want to slap someone rather than help them ...**

----
*
the problem was caused by resizing my superview After placing all of the subviews.
so what to do to fix it, is resize from the top down.
*

Not defending OP in any way, but this bit of information was informative to me. I just spent several hours and came to the same conclusion. Can anybody explain why this is so?
----
My guess would be that two independent mechanisms are meeting each other at a bad time. The first is Cocoa's resizing, which says "resize view, then use the subviews' autoresizing constraints to fit them to the new view size". The subclassed box here says "figure out how big the subviews are, then resize the view to contain them". When you put those together, you end up resizing the subviews immediately after you just fit the superview to them.

The three solutions I can think of for this (all untested):

*Disable resizing subviews. If the view changes, resize subviews yourself.
*Cleverly play with autoresizing that changing the view size doesn't screw anything up. This may not be possible but if you try, remember than for most of Cocoa's views the origin is at the bottom-left corner.
*Size the subviews to the superview instead of vice versa. (This is how NSScrollView and such work, BTW.)


And this page could really use some refactoring. And since you've managed to reproduce the problem... --JediKnil

