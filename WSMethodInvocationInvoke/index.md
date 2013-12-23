---
layout: page
title: WSMethodInvocationInvoke
---

Greetings:
   I'm taking baby steps here.  All I want to do is to call General/WSMethodInvocationInvoke() without parameters and get any response.

Here's what I have:
    
@try { 
        // I. Define the location of the SOAP service and method. 
           // Create a method invocation. Parameters:
           // (1) URL to the SOAP service; 
           // (2) the name of the SOAP method to call; 
           // (3) a constant to specify the SOAP2001 protocol; 
        soapCall = General/WSMethodInvocationCreate((General/CFURLRef)soapURL,         // (1)
                                            (General/CFStringRef)theAction,    // (2)
                                            kWSSOAP2001Protocol);      // (3)
        
       
         
        // II. Set up the parameters to be passed to the SOAP method.
        
        // III. Make the call and parse the results.
        
        theResultDict = (General/NSDictionary*)General/WSMethodInvocationInvoke(soapCall); 

        // If the results are a fault, display an error to the user with the fault 
        // code and descriptive string 
        if (General/WSMethodResultIsFault((General/CFDictionaryRef)theResultDict)) { 
            General/NSRunAlertPanel(General/[NSString stringWithFormat:@"Error %@", 
                             [theResultDict objectForKey: (General/NSString*)kWSFaultCode]], 
                            [theResultDict objectForKey: (General/NSString*)kWSFaultString], @"OK", @"", @""); 
        } else { 
            // Otherwise, pull the results from the dictionary, 
            // held as another dictionary named "soapVal" 
            General/NSDictionary *dictionary = [theResultDict objectForKey:(General/NSString*)kWSMethodInvocationResult]; 
            
         } // end if().


This is merely a shell.
I've launched this in Xcode 3.1 (beta) using Leopard OS 10.5.2.

The code HANGS at General/WSMethodInvocationInvoke().

I was expecting an error or a nil reply or something.

Any ideas?

Ric.
