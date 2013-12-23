---
layout: page
title: HowToRegisterURLHandler
---

**Overview:**
----

Registering a URL handler with your application will allow it to respond to users clicking on a link that uses the registered protocol. For example, an web browser would register a URL handler for the "http", or another example would be an application registering a custom protocol "myapp" which allows the application to handle any clicks on links using that protocol.


**Related Topics:**
----

[Topic]



**How it works:**
----

Here is what you need to do to register your app for a custom URL scheme (for the example we will use a "myapp" scheme).

1) In your Info.plist, add a new entry for CFBundleURLTypes:

    
 <key>CFBundleURLTypes</key>
 <array>
 	<dict>
 		<key>CFBundleURLName</key>
 		<string>MyApp's URL</string>
 		<key>CFBundleURLSchemes</key>
 		<array>
 			<string>myapp</string>
 		</array>
 	</dict>
 </array>


2) Somewhere in your application's startup code (e.g.     init), add this code:

    
 - (void)registerMyApp
 {
 	NSAppleEventManager sharedAppleEventManager] setEventHandler:self andSelector:@selector(getUrl:withReplyEvent:) forEventClass:kInternetEventClass andEventID:kAEGetURL];
 }
 
 - (void)getUrl:(NSAppleEventDescriptor)event withReplyEvent:(NSAppleEventDescriptor *)replyEvent
 {
 	NSString *url = [[event paramDescriptorForKeyword:keyDirectObject] stringValue];
 	// Now you can parse the URL and perform whatever action is needed
 }



**Related Tidbits:**
----

In Mac OS X Tiger and later, you can call [[LSSetDefaultHandlerForURLScheme to register an app as the default handler for a protocol, as would be need for a common protocol such as http, or ftp.

