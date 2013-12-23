---
layout: page
title: CGShadingInMoreDetail
---



Hi,

Just getting to grips with CGShading, and drawing some very simple shades.

One thing has hit me as pretty weird and backward however, now there is likely something that I am overlooking, but when you have to return the Color function that takes certain inputs and creates outputs it's kind of set in stone. What if I want to change this at run-time, for example, to fade the shade starting color to another color over time smoothly. It's not possible is it? Because as soon as I create the CGShading thing I need to supply it this inline function, and that's that... set in concrete.

So there's no way to alter the color function of a shading at runtime?

Thanks for clearing this up! Much appreciated,

- Peter

----

I've never used CGShading, but I've read sample code and from my perspective, it would seem that the inline function is likely evaluated whenever the shading is drawn. This would mean that your inline function could probably use one or more globals to modulate its colors. Of course, globals suck, but hey. --ShamylZakariya

----

Hmm so it looks like fading a shade color smoothly over time is nigh on impossible then? Why has Apple chosen to make access to CGShading (really quite a fundamental part of anything but the most basic of onscreen graphics) so damn clumsy?

Why can't it be like Java2D gradients, which are easy as 123?

- Peter
----
Hmm, Maybe I do not understand what you want to achieve specifically, but CGFunctionCreate accepts an *info* parameter which is supplied to your callback. Hence, it would be quite easy to wrap the CG shading functions and the evaluation callbacks as dynamic Objective-C objects. BTW, What *inline* function? The callback?
----

Ah I had not thought that the info parameter could be used in this way, perhaps this is where I should be looking. I'm just a student, so I apologise if my questions seem ill-explored or thought out. As for the inline function, yes this is my callback.

I don't understand totally what you mean by "wrap the CG shading functions and the evaluation callbacks as synamic Obj C objects". At the moment I set up my CGShading in a normal obj C method in my class, and that references my callback, which is a simple inline C function at the top (outside the implementation). How would I wrap these into a dynamic Obj C object, and how would I then use it from there? You can probably tell... I'm confused by this bit :) It would be great if you were able to shed some light on it for me :)

Thanks,

- Peter

----

If you want to modulate the gradient over time, you will have to use a timer or a thread to cause repeated calls to redisplay. For each call, you'll change the gradient parameters. Apple isn't making anything hard here. This is standard animation 101. --SZ

I just realized I sound a little snarky above. If you're familiar with Java2D then you probably know the basics of animation. The thing is, Apple isn't making things hard on developers, it's just that the really snazzy parts of Quartz graphics can really only be used through the CG C-APIs. Perhaps Apple will make nice Cocoa wrappers for them, perhaps Apple won't. As C APIs go, however, they're petty straightforward, thought the documentation is sorely lacking. --SZ

----

> *How would I wrap these into a dynamic Obj C object, and how would I then use it from there?*

Well, wrapping the CGShadingRef is pretty easy as it is just an opaque CG type. Both the Radial and Axial shaders have a number of common properties, so you could even handle these in a  common base class. When your class creates the shading object, say, in a draw method, you could pass *self* in the CGFunctionCreate *info* parameter. In the private and static evaluation callback, using self, you could turn around and call through to a delegate (*using a pre-obtained function pointer from the delegate here would be better as this will be called often and should be fast*) to do the dirty work. I would also have a 'dirty flag' around so that I could determine if I need to recreate the shader in the draw method, or re-use the existing one (if available) from the last draw invocation.

There is a number of ways this could be packaged, the point is, the CGShading APIs are far from static.

----

Also, a callback function cannot possibly be inline. It's basically the opposite of inline. Inline functions are compiled in place wherever they're called, whereas a callback function isn't known until runtime.

----

Sorry, I must have become muddled with my definitions of an inline function - as I said, I'm still a student and learning the deeper complexities of Obj C (and C too) daily. Thanks for everyone's assistance here, I have yet to coax CGShading into achieving what I want, but I am now solidly reassured that it is possible.

The only thing I could possibly ask for is for a link (if possible) to any information on wrapping plain C functions into an Obj object, and the general benefits/reasons that this is done.

Thanks again,

- Peter
----
Well, that is a bigger question than I am going to address here, but I am sure others will take it to task. In the case of CGShading, as you said yourself, the docs may not be exactly clear to those have not torn the API apart before, both by studying the headers (which also contain a wealth of documentation) as well as in practice. The benefit would be a reusable set of CGShading **objects** that encapsulates CGShading and all of the little details. The next time you need to perform a shading, you can pull your little gem out of your toolbox and be done with it in a snap. This hold true of any of the object oriented languages. It leads to better application design and cleaner code, as your code (which is supposed to be focused on doing the users will; and not be worried about shading) will not be cluttered with all the bits and pieces of maintaining a CGShading sequence or whatever other low-level API that may be.

The virtues of OOP have many other facets, that little rant was a snapshot of a single facet.

----

Hi, 

sorry to bring this up again, but I still have difficulty understanding how to use the callback function effectively... for instance, let's say I need a shade to go from a deep blue to a light blue, I have written my callback function:

    

void BlueShade (void* info, float const* inData, float* outData)
{
   static float deepBlue[4] = { 0.13f, 0.44f, 0.83f, 1.0f };
   static float lightBlue[4] = { 0.65f, 0.96f, 1.0f, 1.0f };
   
   float a = inData[0];
   int i;
   for( i = 0; i < 4; i++)
      outData[i] = (1.0f-a)*deepBlue[i] + a*lightBlue[i];



Great, this function gives me the Blue shade I want... but I am now at the point where I want several OTHER shades, say deep orange to light orange, or deep transparent gray to light transparent gray - you get the idea. At the moment, I know of only one way of doing this... write an individual callback function like the one above for each and every shade I want/need.

But this strikes me as very clumsy... I'm thinking it would be great to have a way to just specify to the function above somehow what to use as the starting color array and the end color array, and this would give great flexibility. But this is not possible because the function is cached and so set in stone when it's provided right? SO how do I achieve what I want to achieve.

Is this what the "void* info" parameter is for? This confuses me also, info is a pointer to void? What does this mean and what is it for?

Thanks for your extreme patience in helping me out with this one, it's totally alien to anything I know of.

----

the "Info" parameter is a pointer to whatever you want it to be. When you declare the shading callback and use CGFunctionCreate, you can pass any object as the info parameter. This object could be a struct, could be a C++ class, could be your Objective-C view or controller object. You'll have to cast it to a void pointer (void*).

In your callback, you can then "un-cast" it back to whatever it was. Be careful, you have to be certain you're castings are safe.

For example:

    

void BlueShade (void* info, float const* inData, float* outData)
{
   MyControllerObject *controller = (MyControllerObject*)info;
   static float deepBlue[4] = { 0.13f, 0.44f, 0.83f, 1.0f };
   static float lightBlue[4] = { 0.65f, 0.96f, 1.0f, 1.0f };
   
   if ( [controller foo] )
   {
      float a = inData[0];
      int i;
      for( i = 0; i < 4; i++)
         outData[i] = (1.0f-a)*deepBlue[i] + a*lightBlue[i];
   }
   else
   {
      float a = inData[0];
      int i;
      for( i = 0; i < 4; i++)
         outData[i] = (1.0f-a)*lightBlue[i] + a*darkBlue[i];
   }
}



So, somewhere in your code you're calling CGFunctionCreate to set up your shading function, right? Well, for the info parameter pass some kind of controller object. Alternately, you might just pass the address of a stepping integer, or a float or whatever. Just be certain that your casts are safe.

--ShamylZakariya

----

That's fantastic, thanks Shamyl - you've cleared up so much in that last post, I can see how easy it is to achieve what I want now. Thanks for taking the time to reply (and reply... and reply :) ) you've made me very happy, thanks again for your patience!

----
Does anybody know why calling CG set shadow does absolutely nada when drawing a  gradient?  The best I've been able to do is get the shadow to show up for a CGPath, but that's not good for me because the path merely traces the outline of by shaded area.
*You draw a gradient by filling the current clipping area, so the shadow falls outside the clipping area and isn't drawn. AFAIK the only solution is to do some drawing with the path to cause the shadow to be drawn before you draw the gradient.*

----
I've written up a gradient class that does alot of that CGShading stuff. If you want, you can check out it's code here:
http://blog.oofn.net/2006/01/15/gradients-in-cocoa/

--Chad Weider

