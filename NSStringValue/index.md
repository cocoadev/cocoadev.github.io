---
layout: page
title: NSStringValue
---

I have a class method to get and set a string which returns the string called "Long". eg:

    
-(NSString *)type
{
     return type:
}

-(void)setType:(NSString *)aType
{
   [ aType retain];
   [type release];
   type =aType;
}


The above is straight forward. I want to know how you call the string value in another method

    
-(double) myValue
{
   if (type = [whatever the type is "Long" ]{
     
      return 0;
    }else{
      return 1;
    }
}


If type is set to a string called "Long". What is the  objective -C code to give back the string value "long" in the MyValue method. 

Thanks DSG

----

It is not clear what you're asking, or what you'd like the code to do.  My guess is that you want     myValue to return 0 if     type is     @"Long", and 1 otherwise?

If so, the     myValue method should look like this:

    
-(double) myValue
{
   if ([type isEqualToString:@"Long"]) {     
      return 0;
    }else{
      return 1;
    }
}



Thanks! works like a charm!

