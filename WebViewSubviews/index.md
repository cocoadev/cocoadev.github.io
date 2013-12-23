---
layout: page
title: WebViewSubviews
---



I know the answer might be quite simple, but I can't seem to be able to figure it out... I have a General/WebView in which I load a web page with 2 fields and a button. My question is if there is any way to access directly those fields to create something like a Autofill for the login & password...

Thanks for all replies.

Charlie

----

I'd suggest doing it via General/JavaScript. Check out General/WebView's stringByEvaluatingJavaScriptFromString: method.

----

For a similar application, I use DOM objects in General/WebKit to access or manipulate html tags as cocoa objects. This is a "bridge" which translate DOM access javascript methods in obj-C style methods.

An exemple :

 <nowiki>
#import "Contoller.h"
#import <WebKit/WebKit.h>

@interface Contoller(internal)
   BOOL formIsSubmitted;
@end

@implementation Contoller

- (void)awakeFromNib
{
   // identification html form is not submitted
   formIsSubmitted=NO;

   // loading of www.site.com site
   General/webview mainFrame] loadRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:@"http://www.site.com"];
}

// delegate method of webview
- (void)webView:(WebView *)sender didFinishLoadForFrame:(WebFrame *)frame
{
   DOMDocument *htmlDoc;
   DOMNodeList *nodeList;
   DOMNode *idenIinput, *passInput, *form;

   // identification html form was submitted : no more thing to do
   if (formIsSubmitted) return;

   // init
   idenInput=nil;
   passInput=nil;
   form=nil;

   // access to the DOM tree of html document
   htmlDoc=[frame DOMDocument];

   // finding INPUT named "theAccountName" (iden)
   nodeList=[htmlDoc getElementsByName:@"theAccountName"];
   if ([nodeList length]>0) idenInput=[nodeList item:0];

   // finding INPUT named "theAccountPW" (password)
   nodeList=[htmlDoc getElementsByName:@"theAccountPW"];
   if ([nodeList length]>0) passInput=[nodeList item:0];

   // finding FORM named "form" (form which contains the 2 input fields)
   nodeList=[htmlDoc getElementsByTagName:@"form"];
   if ([nodeList length]>0) form=[nodeList item:0];

   // all is found
   if (idenInput && passInput && form)
   {
      // setting values (iden and password) of the 2 input fields
      [idenInput setAttribute:@"value" :@"an_id"];
      [passInput setAttribute:@"value" :@"a_pass_word"];

      // then submit of form
      formIsSubmitted=YES;
      [form submit];
   }
}
@end
</nowiki>

General/MrBru
