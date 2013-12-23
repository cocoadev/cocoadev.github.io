---
layout: page
title: DifferenceBetweenDotHAndDotMFiles
---

Not too much to it here. 

.h is the header file (also known as an interface file) where you can declare your action and outlet methods as well as any methods that you will create yourself.

.m is the implementation file where your real code is. It contains your methods and you can call any of the outlets that are declared in your .h. You can easily import extra headers to gain extra methods and outlets.

Think of the .h file as how you advertise your services to the world. Since folks need to include your header file to use your methods (at least use them without compiler warnings), that should be where you keep your public declarations.  Keep private stuff and implementation details in your .m file.

Back to HowToProgramInOSX

