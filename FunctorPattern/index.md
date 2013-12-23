---
layout: page
title: FunctorPattern
---

Functors are small objects that encapsulate an algorithm, almost like a function-turned-object. Define a class for each of the operations that you can apply to your data set, make the method that performs the calculations take an array of values and return an array with the new values (or the same array if the calculation is in-place). The advantage with functors over having named methods or functions that encapsulates the algorithm is that you can use the power of polymorphism, you don't need to know which algorithm you are applying to your data set, you can add new algorithms at any time without changing the code that applies them to your dataset. Functors can also be chained easily (the output of one becomes the input of another, essentially creating a new functor).

Some basic pseudo-code:
    

// this class is the abstract superclass of all functors, could also be a protocol
class Functor {
	Array apply( Array indata ) { return data; }
}

class Mult2Functor : AbstractFunctor {
	Array apply( Array indata ) {
		Array outdata = new Array();

		foreach ( item in indata ) {
			outdata.add( item * 2 );
		}

		return outdata;
	}
}

main( ) {
	Array data = ...; // get data somehow

	Functor f = new Mult2Functor();

	// notice that the code below doesn't need to know which algorithm will be applied

	// outputs a list of all items in "data" multiplied by two
	print f.apply(data);

	// outputs a list of all items in "data" multiplied by four
	print f.apply(f.apply(data));
}


Define a class for each algorithm you need. Create an interpreter for the command line syntax that maps the name of a function to a functor class, instantiate the class and possibly initialize it (there could be a Multiply-functor that could be initialized with a value to multiply by, instead of defining a Mult2Functor, Mult3Functor and so on) and apply it to the data set. No part of the code besides the interpreter will need to know about which functors are available. That's very nice OO.

The FunctorPattern is from the GangOfFour book. See also http://c2.com/cgi/wiki?FunctorObject.

-- TheoHultberg/Iconara

----

To examine a design analysis that settled on the FunctorPattern see FunctorPatternDesignAnalysis

