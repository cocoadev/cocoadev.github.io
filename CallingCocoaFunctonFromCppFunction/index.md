---
layout: page
title: CallingCocoaFunctonFromCppFunction
---



Hi again, it's dedicated thread for problem i've asked a little later. As you can see, a have three class, one obj-c and two of cpp. If a bind my gui (cocoa) button to call UPDATE function of CLASS1 at once - it's all drawing fine! But if i call first FUNC1 from CLASS2 and so on, as a result i'll come to UPDATE function of CLASS1 too!, but i can't see that something is drawing.. Mb you can help, is i can be an error somewhere? (in  passing CPP classes mb)

    

//////////////class 1 ---

@implementation Class1

- (void)UPDATE
{
  NSImage *imageFromBundle = [[NSImage alloc] initWithContentsOfFile:@"/mybmp.bmp"];
  
  if([imageFromBundle isValid]) printf(">>> imageFromBundle valid\n");
  
  if (imageFromBundle)
  {
    [output setImage:imageFromBundle];  //NSImageView* output in header
    [imageFromBundle release];
    
    //[output setNeedsDisplay:YES];
  }
  else
  {
    NSLog(@"File could not be loaded.");
  }
}

@end

//////////////class 2 ---

int Class2::FUNC1 (...)
{  
 
  Class3* _class3 = new Class3();
  _class3-> FUNC2(data);

  return 1;
}


//////////////class 3 ---

int Class3::FUNC2 (...)
{  
  Class1* _class1;
  _class1 = Class1 alloc] init];

  [_class1 UPDATE];

  return 1;
}



----
[[PostYourCode might be a little ambiguous as to what to show and what to remove, but I'm pretty sure this ain't it.

You have replaced all of the interesting bits of your code with vague comments, so we have no idea what you're doing. PostYourCode for real.

----
Sorry, i've changed code. I thought those code is not principal for work processes...

----
Any ideas?..

----
You're just creating an object out of nothing and then tell it to draw.... Where do you think it's going to draw?

I suspect that you want to talk to an existing object but you don't know how, so you've created a new one but that won't work. You'll probably want to read up on MakingNibsTalkToEachOther.

----
Sorry, but are you talking about  output in ([output setImage:imageFromBundle];)? If so it's an NSImageView object, defined in header..  If about Class1* _class1; so application is coming in UPDATE func.. Or i've missed something, or dont understand... Reading doc didn't helped me to understand too.. Can you on my example show what to change?

----

MailingListMode

----

No, I can't show you what to change on your example because it's a conceptual error.

You want to display an image in a view which is in a window. This means that you need to get a reference to that view. You have a controller for the view, so you have to get a reference to that controller. What you do instead is create an entirely *new* controller which has no references to anything. The variable is declared as type NSImageView but it's not pointing to anything useful.

----
Well, as i understand you are talking about   IBOutlet NSImageView *output; But it's "real" controller referensed to gui. As i've sad "bmp" is drawed, but in single function calling, not trought cross-class calling. So it's working.. I'm talking about cross-class funcion calling, not about NSImageView reference, i suppose it's all allright with it.. Or did't understant smth again) Are you talking about NSImageView *output? And do you thing it's not connected to nib? Or what..
----
When you do [[Class1 alloc] init], you're creating a whole new Class1 instance. This is not an instance that exists in a nib with connections dragged around � its instance variables will normally be initialized to nil. Unless there's something you're not showing us, [_class1 output] == nil.

