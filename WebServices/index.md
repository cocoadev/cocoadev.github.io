---
layout: page
title: WebServices
---

Web Services  - See General/WebServicesCore

Hello,

I'm using General/WebServices framework to make some XMLRPC calls. For one of the rpc methods the response is an array containing strings.
The problem appears if one of the elements of the array is an empty string. In this case General/WSMethodInvocationInvoke returns an error saying "could not determine type of array element".
The response array looks like this:
                 <array>
                   <data>
                     <value>
                       blabla
                     </value>
                     <value>
                     </value>
                   </data>
                 </array>
The problem is the second element of the array. For the moment the only solution is to take General/DebugInBody and put in <value></value> a dummy value and deserialize again. Conforom to XMLRPC protocol if no type is specified for a value it defaults to string. In this case it should be an empty string.
A better solution it will be to use General/WSMethodInvocationAddDeserializationOverride and add my custom deserialization but I can't find any examples and the Aplle's doc it's really, really poor at this chapter.

Any thoughts?
