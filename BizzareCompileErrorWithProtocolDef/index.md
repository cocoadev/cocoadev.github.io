---
layout: page
title: BizzareCompileErrorWithProtocolDef
---

I've moved some method declarations out of the @interface block into a @protocol block for Objective-C++ issues (long story).  I have a file with just

    
@protocol WrapperProtocol

... some method declarations ...
- (float*)descriptorOfPixels:(unsigned char *)I withHeight:(int)H andWidth:(int)W numChannels:(int)numChan atPoint:(NSPoint)P;

@end



When compiling this I get  "error: parse error before '(' token".  This never happened when the declaration was in the interface block. Now if I change the "I" (letter 'eye' not number one) to "pixels" or "J" or seemingly anything else
i.e. 

    
@protocol WrapperProtocol

... some method declarations ...
- (float*)descriptorOfPixels:(unsigned char *)notI withHeight:(int)H andWidth:(int)W numChannels:(int)numChan atPoint:(NSPoint)P;

@end



everything compiles just fine.  Am I missing something painfully obvious (not for the first time)? Is the compiler encouraging me to choose more descriptive variable names? Obviously I can live with this, but any insight would be appreciated.

----
Preprocess the file. I bet I is #defined to something that's screwing up the compiler.

