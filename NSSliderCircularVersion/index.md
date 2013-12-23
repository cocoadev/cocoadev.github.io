---
layout: page
title: NSSliderCircularVersion
---

Hi, been playing with the new NSSlider in Panther, the little circular one. I can't find any documentation that would allow me to do the following...

- There's no way to dictate that the circular slider should *stop* when it reaches its maximum value, it just instantly goes back to its min value and starts counting up again as you slide. This makes it almost impossible to quickly use the circular NSSlider to set it to the maximum value. Please tell me I am wrong about this, the cool new slider is useless to me without this.

- I can't find a way to have it return only integer values without including the ugly tick-marks, is there a way to have my cake *and* eat it?

Let me know what you guys are thinking about this new widget,

-Peter

----

The following code works just fine for me using an circular NSSlider without tick marks:

    
    int intValue = [_testSlider intValue];
    float floatValue = [_testSlider floatValue];
    
    NSLog(@"intValue = %i", intValue);
    NSLog(@"floatValue = %f", floatValue);


Giving me:
    
2003-11-18 20:30:07.640 SliderTest[412] intValue = 50
2003-11-18 20:30:07.641 SliderTest[412] floatValue = 50.000000
2003-11-18 20:30:07.781 SliderTest[412] intValue = 46
2003-11-18 20:30:07.781 SliderTest[412] floatValue = 46.101044
2003-11-18 20:30:07.834 SliderTest[412] intValue = 42
2003-11-18 20:30:07.835 SliderTest[412] floatValue = 42.620819
2003-11-18 20:30:08.581 SliderTest[412] intValue = 39
2003-11-18 20:30:08.581 SliderTest[412] floatValue = 39.758362


About the maximum value, maybe the circulat NSSlider  is designed to be used as Apple does in the Font panel, to select a shadow angle.

JacobHazelgrove

----

Another circular slider issue: if I set a circular slider to Regular size in IB under Tiger, I get a Small one scaled up and blurry. It's locked at 28 x 30 pixels, which documentation says is the minimum size for a regular one. Is there some voodoo that�ll get it do do the right thing (e.g. setting the size to some magic value programmatically)?

----

Isn't the whole point of the circular slider that it's, well, circular? It's for setting and indicating values that wrap around, like direction. If you need something that doesn't wrap, you should probably stick with the linear slider.

