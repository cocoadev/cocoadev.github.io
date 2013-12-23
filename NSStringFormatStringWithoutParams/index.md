---
layout: page
title: NSStringFormatStringWithoutParams
---



Hi , all!
I have looked on NSString documentation . And trying to use the NSString with the Format string.
I have a piece of code , and want to know how it works .
Usually , we use our NSString like this , NSString *aString = [NSString stringWithFormat:@"XXX%@XX%dX...",params1,params2,...];
if I don't pass in the parameter , what will happen? 
     
NSString *aString = [NSString stringWithFormat:@"%@"];
NSLog(@"%@",aString);

this piece of code crashed app.
If I change something in the code , making it like this
     
NSString *anotherString = [NSString stringWithFormat:@"%@",@"testing"];
NSString *aString = [NSString stringWithFormat:@"%@"];
NSLog(@"%@",aString);

this time , the app doesn't crash , but it prints "testing"
I don't pass in anotherString to NSLog , but the log prints the "testing". It's really kind of weird . 
Can some one give me some explanation ?
thanks a lot.
--Daniel
----
Why can't you just do     NSString *anotherString = @"%@";?

----

You need to look into how varargs works and there are no portable assumptions which can be made about it.  Essentially, you are getting unpredictable behaviour since the underlying function is trying to use arguments which you didn't pass into it.  As I understand varargs, the structure is usually some list of pointers to the values you are going to read in the callee.  If you don't actually pass any varargs in, the callee will try to read whatever happens to be in the register which points to the base of the structure or the memory address where the base of the structure should be (details depend on the architecture and ABI).  Since you aren't providing the arguments, the data it will see could be considered uninitialized memory (or register data).  The reason why you are seeing different behaviour in the two cases is because the state of the registers and memory which you aren't initializing will be whatever they were after the previous operation.  If you change what the previous operation is, you change what is left in the uninitialized location.

Does that make sense?

--JeffDisher
----

yep.I think it's really a dangerous behavior.--Daniel

----
Welcome to C: if you make the wrong move, your app will crash or, worse, silently corrupt your data. If you don't like that sort of thing, this is probably not the language for you.

----
This stack-smashing is such a common occurrence in straight C that the gcc guys introduced lint-checking for     printf, so that if the number or type of the arguments to     printf does not mesh with the field codes in the format string, gcc will warn you.  I wish the same feature were present for     NSString and     NSLog().

