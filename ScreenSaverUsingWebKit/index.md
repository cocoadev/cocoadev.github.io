---
layout: page
title: ScreenSaverUsingWebKit
---



Hey, this might be beyond my current ability in Cocoa, but I found myself wondering if the WebView from WebKit could be loaded in such a way as to use it with the screen saver module template in XCode.  I see that the template doesn't include a nib (understandable) but thus far I haven't yet learned how to load a view programatically.  Is WebView even able to do what I'm thinking (just load & display a full screen page from a URL as a screen saver)?

CliffPruitt

----

Remember, a nib is just creating objects the same way that you create them, and then saving them for later. You should be able to do this by simply alloc/initing a WebView, inserting it into your screensaver view, and then telling it to go to a URL.

----

And, although the template doesn't have a nib, there's nothing stopping you from adding one.

----

Hmmm.... I wouldn't even know where to begin with adding a nib to a template that didn't have one.  Its a slow learning process for me.  Maybe I'll tackle that one a little later after I have a better handle on things...

Thanks for the insight though.  I never thought of being able to add a nib to a template that didn't have one.  I guess I figured if it didn't have one included that nibs couldn't be used in that type of app.

- CliffPruitt

*you don't have to add the nib to the template, just add it to the project. create the nib in IB, save it in your project directory, then add it in XCode - Project menu > Add To Project. Although you don't need to do that in this case.*

----

    
// our header
#import <ScreenSaver/ScreenSaver.h>
#import <WebKit/WebKit.h>

@interface WebSaverView : ScreenSaverView  {
    WebView *webview;
}
@end


// the .m file:
#import "WebSaverView.h"

@implementation WebSaverView

- (id)initWithFrame:(NSRect)frame isPreview:(BOOL)isPreview {
    self = [super initWithFrame:frame isPreview:isPreview];
    if (self) {
        webview = [[WebView alloc] initWithFrame:frame];
        [self addSubview:webview];
        
        webview mainFrame] loadRequest:[[[NSURLRequest requestWithURL:[NSURL URLWithString:@"http://www.apple.com"]]];

        
        //[self setAnimationTimeInterval:1/30.0];
    }
    return self;
}

... etc..

@end

- GusMueller

