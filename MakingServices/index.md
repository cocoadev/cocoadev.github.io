---
layout: page
title: MakingServices
---

First of all, you may want to read up on the StandardService concept so you can be certain that you know what you're trying to do here.

There are three main parts to an application that provides services (excepting any non-service-related parts of the application, of course):


* the main function, which registers that there are services available, and what object the methods are contained within.
* the object that provides the methods that the services call (and also any objects that it makes calls to).
* the Info.plist which tells the system the names of the menuitems, what methods they should call, and other information about them.


I'll cover all three in this page.

**The Main Function**

In an application that doesn't vend services to other applications, the main function is typically just used to make your main AutoReleasePool, and start the current RunLoop.

In an application that *does* vend services, however, you're going to have just a little bit more code.

Basically, you have to:


* allocate and initialize the object that handles the requests for services
* register that object as a service provider
* configure the current RunLoop as a service provider
* release the service handling object


That's right, four lines of added code. Not bad, eh?

Allocating and initializing the object that handles the service requests (our class will be called MyServiceProvider) is straight out of a textbook:

    
MyServiceProvider *serviceObject = [[MyServiceProvider alloc] init];


Registering the object as a service provider isn't much more difficult, but it's a rather obscure function call, so if you're going to be making a lot of applications with services, you might want to write it down:

    
NSRegisterServicesProvider(serviceObject, @"CocoaDevServicesApp");


CocoaDevServicesApp is the name of our application.

Configuring the RunLoop as a server is pretty simple, but also pretty obscure. Note that this line is placed after NS_DURING, ie. in the exception catching block (the line you'll add is commented, the others are provided for clarity):

    
NS_DURING
	[[NSRunLoop currentRunLoop] configureAsServer]; // this is it!
	[[NSRunLoop currentRunLoop] run];
NS_HANDLER
...


Finally, where the AutoReleasePool is itself released, you add in a simple line to release your service handling object(again, further lines provided for clarity):

    
[pool release];
[serviceObject release];
...


And your application can vend services.

Or, if you're a lazy application author like I, MikeTrent, am, you can just add this to your application delegate's applicationDidFinishLaunching: method:

    
    [NSApp setServicesProvider: serviceObject];


If you do this, you don't have to call NSRegisterServicesProvider or monkey around with creating your own event loop. The above steps are great for command-line tools and daemons that need to register services, but is a bit overkill for us lazy app authors. For an example, see the graphic man page Xmanview available at http://mosxland.sourceforge.net/ -- MikeTrent

**The Service Request Handler**

The next thing that you need is a class to handle requests to your application for services. You'll recall that in the main function, we had an object called serviceObject that was of class MyServiceProvider. So, we're going to create MyServiceProvider now.

First of all, the class interface is fairly simple:

    
@interface MyServiceProvider : NSObject
@end


If your services were very complex indeed, you might do something more complex with this, but it probably wouldn't make much sense to declare any instance variables, and method declarations aren't needed either.

Next, the implementation (the good bit!). Our service providing example isn't that big of a deal, so what we're going to do is make a service that capitalizes the selected string.

    
- (void)doCapitalizeService:(NSPasteboard *)pboard
                   userData:(NSString *)data // typed as what we handle
                      error:(NSString **)error
{
	NSString *pboardString;
	NSString *newString;
	NSArray *types;

	types = [pboard types];
	
	// if there's a problem with the data passed to this method
	if(![types containsObject:NSStringPboardType] ||
       !(pboardString = [pboard stringForType:NSStringPboardType]))
	{
		*error = NSLocalizedString(@"Error: Pasteboard doesn't contain a string.",
		@"Pasteboard couldn't give a string.");
		// if there's a problem then it'll be sure to tell us!
		return;
	}

	// here is where our capitalizing code goes
	newString = [pboardString capitalizedString]; // it's that easy!

	// the next block checks to see if there was an error while capitalizing
	if(!newString)
	{
		*error = NSLocalizedString(@"Error: Couldn't capitalize string $@.",
		@"Couldn't perform service operation.");
		// again, it lets us know of any trouble
		return;
	}

	// the next bit tells the system what it's returning
	types = [NSArray arrayWithObject:NSStringPboardType];

	[pboard declareTypes:types
	               owner:nil];
	
	// and then this sets the string to our capitalized version
	[pboard setString:newString
	          forType:NSStringPboardType];

	return;
}


And that's what you have to do on this end. Note that if you don't return a value, you can skip the bits after the second if loop, but before the "return;" line.

**Info.plist**

Every Cocoa application has an Info.plist file that tells the system a little bit about it. Inside it are things like the bundle identifier, development region, executable, package type, and the like.

Our app is called CocoaDevServicesApp, so we're going to change the string in CFBundleName to read CocoaDevServicesApp. If you're reading the straight text (without ProjectBuilder's sprucing up available in the target editor), then the lines you've changed will look like this when you're done:

    
<key>CFBundleName</key>
<string>CocoaDevServicesApp</string>


That lets the system know what to call our application.

Now, since our app *only* provides a service, and doesn't have any other interface, it wouldn't make much sense for it to have a Dock icon. So, we're going to let it run in the background only:

    
<key>LSBackgroundOnly</key>
<string>1</string>


Now to the *really* fun bit: telling the system a little more about our service. Please also note the NSBGOnly had been replaced with LSBackgroundOnly.

It begins with an array for the key NSServices (this is an array so you can have multiple services in the same application, if you so desire):

    
<key>NSServices</key>
<array>
	<dict>


Next, we define the key equivalent. So, if we want to be able to access our service with a keystroke, say, a simple shift-command-c (the shift modifier is set by the system, so services don't interfere with application menu items), then we do this:

    
<key>NSKeyEquivalent</key>
<dict>
	<key>default</key>
	<string>C</string>
</dict>


Next, we have to tell it what we want the menuitem to be called, and whether we want it to be in a submenu or not. For our example, we're going to use a submenu, just to demonstrate how it's done:

    
<key>NSMenuItem</key>
<dict>
	<key>default</key>
	<string>CocoaDev/Capitalize String</string>
</dict>


When our service is installed, it will show up in the Services menu, under the CocoaDev submenu. If we wanted to forego the submenu, we'd just omit the CocoaDev/ part of the string.

Next, we tell the system what method it has to call when our service is selected:

    
<key>NSMessage</key>
<string>doCapitalizeService</string>


Note that doCapitalizeService is the name of the method we wrote which handles the capitalization. Also note that you're omitting the colons et al, because they have to be there, so the system can just ask for the main part of the method name.

Next, we supply the port name, which is just the name of our application, in this case:

    
<key>NSPortName</key>
<string>CocoaDevServicesApp</string>


The final thing we have to do is tell it what send types our method, doCapitalizeService, responds to, and to finally close the dict and array:

    
		<key>NSSendTypes</key>
		<array>
			<string>NSStringPboardType</string>
		</array>
	</dict>
</array>


And there you have it. All you have to do is compile and build, install into /Library/Services, and log out and log in again.

If you were writing a full-fledged app, you wouldn't have to install it into the /Library/Services folder, because the system remembers that it provides services after it's been launched the first time. That's how Mail and OmniWeb can offer services without sticking anything inside the /Library/Services folder!

One last caveat: if you're writing an application that only provides services, and is to be installed in the /Library/Services folder, you have to change the setting in the Edit Active Target... window in ProjectBuilder for the file extension from .app to .service, or the system won't recognize it.

**Conclusion**

So, now that you've absorbed all this knowledge about how to write a service, you can add them to your applications, or go out and write small .service's for various tasks! And they're off!

More info can be found on your system at /Developer/Documentation/Cocoa/ProgrammingTopics/Services.pdf, and an example, which I learned from, and whose code I blatantly, um, borrowed for the example herein, is at /Developer/Examples/AppKit/SimpleService.

-- RobRix, with thanks to Apple/NeXT, and especially Ali T. Ozer

----

**Localisation of Service Menu entries**

An Issue I ran into a few days ago was how to localise the Services Menu entries. Apple seems not to document this (or I failed to find it), and simply adding InfoPlist.strings to <Bundle>/Contents/Resources/<language_code>.lproj/ (e.g. <Bundle>/Contents/Resources/de.lproj/InfoPlist.strings for German) did not do the trick. After Poking around for a while and some googling I found out how this is done:

1. Inside your Bundle's Info.plist, name the menu entries as described above

    
<key>NSMenuItem</key>
<dict>
	<key>default</key>
	<string>Some Service</string>
</dict>


I suspect this entry will be used as default if no localisation for a particular language exists, but I haven't checked that.

2. For each language you'd like to add, create the usual <lang_code>.lproj directory inside your <Bundle>/Contents/Resources/ directory, if not done already.
(e.g. en.lproj/ for English, also see http://developer.apple.com/documentation/CoreFoundation/Conceptual/CFBundles/Concepts/BundleAnatomy.html and http://developer.apple.com/documentation/MacOSX/Conceptual/BPInternational/Concepts/LanguageDesignations.html) 

3. Create a file named S<nowiki/>ervicesMenu.strings inside each of these directories. Use a text editor that lets you set the encoding. XCode 2 does that.

4. Write your translation(s) in these files like this:

    
// A Comment
"Some Service" = "Ein Dienst";
"Another Service" = "Ein anderer Dienst";
 

All non-comment lines must end with a semicolon.

5. Set the Encoding to **UTF-16** before you save. To do that in XCode 2 right-click (ctrl-click) somewhere in your text, select "Get Info" and open the Tab "General". Change File Encoding from "No Explicit File Encoding" (or whatever is set) to "UTF-16". Also change File Type to "text.plist.strings". Then Save.

That's it. After installation and log-out,log-in again your Service Menu entries will (hopefully) show up in your native laguage. Have fun!

Markus Walther

