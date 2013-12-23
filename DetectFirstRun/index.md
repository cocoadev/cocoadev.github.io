---
layout: page
title: DetectFirstRun
---

see also SetADateForAnAppToExpire for application of this idea

----

My app is using preferences properly and neatly, and I want to detect the first time my app is run by checking for the existence of these preferences. I understand this is a pretty normal thing to do. I can't find a place to put the code that would detect blank preferences though... and to tell the truth, I'm not sure if I have things all sorted out at all.

In my main Controller I have the following...
    
+ (void)initialize //this overides the initialize method for this class
{
   //Create a dictionary...
      NSMutableDictionary *defaultValues = [NSMutableDictionary dictionary];
   //archive the objects to be stored as NSData...
      NSData *lastOpenedPathAsData = [NSArchiver archivedDataWithRootObject:[NSString stringWithString:@""]];
   //put the defaults in the dictionary...
      [defaultValues setObject:lastOpenedPathAsData forKey:PALLastOpenedPath];
   //Register the dictionary of defaults...
      [[NSUserDefaults standardUserDefaults] registerDefaults: defaultValues];
}

And I am trying to check on the awakeFromNib (in the same controller) for existing preferences like so:
    
if ([defaults objectForKey:PALClientList] == nil) 
   [introWindow makeKeyAndOrderFront:self];

But my introWindow is never shown :(

I am assuming that the detection is coming too late - and that if the defaults aren't there, then they are set up as blank, and so PALClientList is never going to be nil.

What order are these things done in:     +(void)initialize,     -(void)init ?

Thanks for any help you can offer!

----

Yes,     initialize is always sent to the class object before the first method. It is documented in the GCC manual.

file:///Developer/Documentation/DeveloperTools/gcc-3.3/gcc/Executing-code-before-main.html

P.S. Please format your code and surround it with BEGIN/ENDCODE markers.

----

Perhaps an easier way would be to add this line to +initialize:

    
[defaultValues setBool: YES forKey: @"IsFirstRun"];


Then, when you want to show your first run window do this:

    
if( [defaults boolForKey: @"IsFirstRun"] == YES ) {
  [defaults setBool: NO forKey: @"IsFirstRun"];
  // Show window here.
}


This just checks to see if the "IsFirstRun" bool is YES (by default it is, so when your app is run for the first time this test will succeed). Then the "IsFirstRun" bool is set to NO, so that the next time your app is run, the test fails, and the window is not shown.

This way you don't have to try to run code before main or anything, which is overkill for such a simple problem.

-- QuentinHill


----
I generally add the following to the awakeFromNib of my application controller, this handles first run and version changes nicely...
    
NSUserDefaults *userDefaults = [NSUserDefaults standardUserDefaults];
NSString *version = [userDefaults stringForKey:@"version"]; //will return 'nil' if no defaults
NSString *appVersion = [[[NSBundle bundleForClass:[self class]] infoDictionary] objectForKey:@"CFBundleVersion"];
if(!version) {
	NSLog(@"First run - configuring preferences");
}
if(!version || ![version isEqual:appVersion]) {
	NSLog(@"Version changed %@ -> %@", version, appVersion);
	[userDefaults setObject:appVersion forKey:@"version"];
}

-- RbrtPntn

