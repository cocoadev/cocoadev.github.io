---
layout: page
title: WSCore
---

General/WSCore is short for Web Services Core, which is an Apple-provided framework for accessing XML-RPC and SOAP web services that ships with Mac OS X. Apple's Web Services Core Programming Guide is located here: http://developer.apple.com/documentation/Networking/Conceptual/General/UsingWebservices/

----



Hi,
Im working on a client written in cocoa and struggle with making valid soap calls with params that contain elements that can occur multiple times using General/WSCore:

Ill try to make myself more clear
I got this output to be made (simplified here)
<order>
	<orderposition>blabla</orderposition>
	<orderposition>blabla</orderposition>
	<orderposition>blabla</orderposition>
	<orderposition>blabla</orderposition>
	<name>lala<name>
</order>

Now, General/WSMakeStubs takes a General/CFTypeRef (so a void*) as Order so I could pass a custom object but I pass a dictionary with the params because that'll get serialized all by itself. The Problem is I cannot add serveral "orderposition" objects in a dictionary. I tried to add an General/NSArray to the Dictionary but that'll not produce the output above but:
<order>
	<array length="4">
		<0>
			<orderposition>blabla</orderposition>
		</0>
		<1>
			<orderposition>blabla</orderposition>
		</1>
		<2>
			<orderposition>blabla</orderposition>
		</2>
		<3>
			<orderposition>blabla</orderposition>
		</3>
	</array>
	<name>lala<name>
</order>

The result of soapCalls made via General/WSInvocation are all General/NSDictionaries with handle multiple elements in the first form just fine - giving me back an General/NSArray (which led me to believe I should fill the Parameters dictionary with one too!)

Im not yet sure I did it right and therefore if it is a bug. Im quite the NOOB when it comes to General/WSCore and hope it is my fault - then its easier to remedy  :) BUT if it is a bug I'd be happy to report it. :)

I also read the docs and know I can add a Callback for my objects... but then I'd have to serialize all values in the custom orderPositions (which would be an array) myself, no? So ... i;d have to serialize every orderPosition and it's values (which could again be General/ComplexTypes)
==> I'd end up having to do almost all serializing myself then!?

Regards,
Dom
