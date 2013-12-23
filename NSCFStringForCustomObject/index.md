---
layout: page
title: NSCFStringForCustomObject
---



So, first of all a newbie disclaimer: I'm new to Cocoa programming, and I've worked through about half of the book of Hillegass.

I've created a class which is composed of a number of other classes, among which there is a custom-made class. I'm creating a simple program that contains my bank transactions as read from the bank statements downloaded from my bank. I have one class called Transaction.m, which contains, among others, an instance of Post, with setter/getter defined as follows

    

NSString *inComing = [[NSString alloc] initWithString:@"in"];
NSString *outGoing = [[NSString alloc] initWithString@"out"];

@interface Transaction : NSObject <NSCoding>
{
Post *thePost;
NSString *inOut;
}
-(Post *)post;
-(void)setPost:(Post *)aPost;
@end

@implementation
-(Post *)post
{
  return thePost;
}

-(void)setPost:(Post *)aPost
{
  [aPost retain];
  [thePost release];
  thePost = aPost;
}
@end



This class is used in an NSArrayController object to display a number of transactions in a document. It is put together using Key value coding.

So far so good. But, when I debug the application, the setPost:(Post *)aPost method received an object for the variable aPost which is of the class NSCFString and not of class Post! This is not such a problem, because the code displayed above works, even though this NSCFString object is not a Post object. But, if I try to call a method on the aPost object inside the setPost method, like inOut = [aPost isIncoming] ? inComing : outGoing; to set the inOut NSString of the Transaction object, I get an error "no selector isIncombing found for NSCFString" (or something along that line). 
This has probably got something to do with the key-value coding behind the scenes organization. How can I get instead of an NSCFString object, a 'real' Post object in my setter method?


----

How and what object do you bind the view through NSArrayController to the "post" key ?
Basically most of the bindings in Interface Builder is done through NSNumber or NSString or NSData,
because IB and the view observing the controller don't know how to handle your Post class !

----

I created a key in the NSArrayController called post, as the post member variable (which is of the class Post) in the Transaction class is the object I want to change. I have no clue how these Bindings work behind the scenes, but apparently it is not as straightforward as I thought or hoped.
I'll try the following tonight:

    

-(void)setPost:(Post *)aPost
{
  [aPost retain];
  [post release];
  post = aPost;
  [self updateInOutString];
}

-(void)updateInOutString
{
  inOutString = [post isIncoming] ? inComing : outGoing;
}



----

I suppose the Post and the Transaction classes are the model classes in the MVC paradigm, is that OK ?
Can I also assume that you're binding through Interface Builder ? 
You can of course bind the view class and your model through NSArrayController 
programatically.  You can do that in IB if you write IB plugin for your custom view class.
But if you don't create the plugin and you're binding the model with an Apple-supplied class like NSTextView,
why can one suppose NSTextView automagically supports your Post class ?
As NSTextView or other classes only know a few classes, you need to provide NSValueTransformer. 

However, It seems to me that you don't need to create your class yourself; NSDictionary with key @"post" may be enough.
From the KVC point of view, there're no difference between this and a class with -setPost and -post.


----
Yes, Post and Transaction are model classes. They are bound in the IB as follows: an NSArrayController class is used to display multiple Transactions in an NSTableView. One of the columns in the NSTableView contains a combobox, which is filled with Post objects in MyDocument.m. Upon selecting a post from the combobox, I'd like another column to update to show if the Post is income or spending. This column is bound to the inOutstring object in the Transaction class.

My suggestion above does not work btw.

----

First, read Apple's docs concerning KVC, KVO, and Binding. You don't need understand every line, but give them a glance.
In order to notify the view of the update, you must assign inOutString using -setInOutString. So you need
    
@implementation
-setInOutString:(NSString*)s
{
[s retain];[inOutString release];inOutString=s;
}
@end

and you need to call -setInOutString inside -setPost.

You also need to use NSValueTransformer to bind a Combobox to the classes you defined yourself.
I think you can bypass this complication by following the apple's Master-Detail example...

