---
layout: page
title: SharingNibsBetweenWindows
---



Hi All,

I'm new to Cocoa and I'm having trouble understanding nib files to some extent. Everywhere I look I only ever see examples of one nib file per window. That for each window in an application you should have one nib where you completely define the content/layout/control of the window. Is there any way to define separate pieces of a window in several different nibs? http://goo.gl/OeSCu

Suppose I have some group of controls that I want to display on several windows within my application. Do I have to draw those controls on every single window separately or is there a way to setup the layout/control of the controls in an NSView in it's own nib and then incorporate the one view into as many windows as I want?

Let me know if you need any further clarification of my question. What I want to do is fairly simple, I think, but I'm having a hard time explaining it. Thanks for any help you can offer me.

----

Do you have to define a window completely? No. You can load an arbitrary nib into an arbitrary object and hook it up to whatever you want ... however you want. You'll probably want to load the nibs into your own NSObject-based controller object rather than try to use/override NSWindowController; but I might be wrong.

For example, I could define a main application with a window, and cut the window up into 3 custom views. I could three views from 3 different nib files and install them in my window. For simplicity I might make my custom views NSBoxes, so I could quickly/easily install the subview in the box; but I think custom views should also work. 

As for practical application I can imagine a case where an application has a specific set of controls it will re-use in different windows, say like movie playback controls.

-- MikeTrent

----

If you look at the Plugged-In Cocoa example here:

http://cocoadevcentral.com/articles/000068.php

You will see an example of loading a View (filled with controls) and adding it to a window. You can effectively ignore the fact that the view comes from a plugin. The point is, given a view (NSBox, etc.) you can easily add it to any other container (NSWindow, another view, etc.)

-- JoeZobkiw

----

Thanks for the quick and helpful feedback. I have one question about using the technique in the Plugged-In Cocoa example. It seems to me that in order for this to work I need to create a placeholder NSView in my window and then add the real NSView to the placeholder. This creates a view nested inside a view. The placeholder view will never really be used after the real view is added to it. Is this inefficient or is this not a problem?

----

I just wanted to post my solution to this problem based on the feedback I received here. I thought if anyone was interested they could review this solution and let me know if this is a reasonable way to approach this problem. Here is this basic code for creating a custom view with a nib file.

    
@interface NibView : NSView
   {
   IBOutlet NSView *_view;
   }
@end


@implementation NibView

- (id)initWithFrame:(NSRect)frame
   {
   self = [super initWithFrame:frame];
   if ([NSBundle loadNibNamed:@"NibView" owner:self])
      {
      [_view setFrame:[self bounds]];
      [self addSubview:_view];
      }
   }

@end


After creating this class I created an empty nib file named "NibView.nib" in Interface Builder. Then I dragged a "CustomView" NSView object from the IB palette to the nib file window. This will create a new window that will show an empty NSView. I resized this new NSView and added some controls to it from the IB palettes. Once I had the NSView built the way I wanted I needed to hook it up with my NibView class.

Next I imported my NibView class into Interface Builder. Then I changed "File's Owner" to a NibView. Then I connected the NSView object that I created in Interface Builder to the _view outlet in the File's Owner. If this were a real class then I would also have other outlets and actions in the File's Owner which I would now connect to the corresponding controls in the NSView. Once I was done connecting everything I saved the nib file. Now I just need to add it to an existing window in my application.

Lastly I open up a nib file in Interface Builder that contains one of my applications windows. Next I imported the NibView class into Interface Builder. Then I placed a "CustomView" object onto my window. Then I changed the "CustomView" to a "NibView". Now when the application loads this window the "CustomView" will be replaced with an instance of NibView. NibView will then load it's own nib file and replace it's contents with the contents of the view in the nib file. Simple right? : )

So I don't know if this technique is very useful to anyone or if it's even advisable, but I found it useful in certain situations. Sometimes I want to create a reusable custom user interface element out of standard IB parts. This is one way to do that.

----

Nice little guide! Really explains how to use views from other nibs. Just one minor correction though - you dont need to have you NibView view inside a window in the second nib. Just create a simple NSView in the nib file, populate with controls etc and hook it up. Also you can use the new NSViewController in Leopard to replace this class entirely. Danny.

----

Leopard does something rather similar with its NSViewController.  Because storing an individual view in a nib is such a common paradigm, especially in preference panes and the like, Apple has introduced a class for this specific purpose, much like what NSWindowController does for windows.http://jamtangankanan.blogspot.com/
http://www.souvenirnikahku.com/
http://xamthonecentral.com/
http://www.jualsextoys.com
http://cupu.web.id
http://seoweblog.net
http://corsva.com
http://bookingbaliaccommodation.com/
http://seminyakbalivillarental.com/
http://www.ubudbalivillasrental.com/
http://www.balivillaexotic.com/
http://thechristinbalivilla.com/
http://www.pusatjamoriginal.com/
http://citralandpekanbaru.net78.net/
http://www.jeparacrafters.com/
http://www.cantikherbal.com/
http://www.kopiluwakbandung.com/
http://tki-mandiri.com/
http://citraindah.biz/
http://www.kostumbadut.info/
http://www.rotankita.com/
http://www.souvenirku182.blogspot.com/
http://keishaflorist.com/
http://www.keishaflorist.net/
http://jaringaniklan.om-onny.com/
http://www.om-onny.com/2012/01/agen-haji-dan-umroh.html
http://www.om-onny.com/2012/02/cara-cepat-meninggikan-badan.html
http://www.om-onny.com/2012/02/cara-cepat-mengencangkan-payudara.html
http://www.om-onny.com/2012/02/cara-mengatasi-insomnia-sulit-tidur.html
http://www.om-onny.com/2011/11/4-cara-mengecilkan-perut-buncit.html
http://www.om-onny.com/2012/01/tips-menghilangkan-stress-di-pagi-hari.html
http://www.om-onny.com/2012/01/4-tips-agar-awet-muda.html
http://www.om-onny.com/2012/02/cara-menambah-berat-badan.html
http://www.om-onny.com/2012/02/tips-cara-memilih-dan-memakai-jilbab.html
http://www.om-onny.com/2012/02/cara-cek-server-hosting-yang-lagi-down.html
http://www.om-onny.com/2012/01/cara-membuka-indera-keenam.html
http://www.om-onny.com/2011/04/buka-aura-dan-cakra-tenaga-dalam.html
http://www.om-onny.com/2012/02/tips-cara-memilih-dan-memakai-jilbab.html
http://yudinet.com/
http://seo.corsva.com
http://seojek.edublogs.org/
http://tasgrosir-brandedmurah.com/
http://upin.blogetery.com/
http://www.symbian-kreatif.co.cc/
http://upin.blog.com/
http://release.pressku.com/
http://cupu.web.id/tablet-android-honeycomb-terbaik-murah/
http://cupu.web.id/hotel-murah-di-yogyakarta-klikhotel-com/
http://www.seoweblog.net/hotel-murah-di-semarang-klikhotel-com/

