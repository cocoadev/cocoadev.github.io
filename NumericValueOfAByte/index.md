---
layout: page
title: NumericValueOfAByte
---



Okay, I want something simple, but yet, I can't find the appropriate method to do it.

I have a NSData with data in it, several thousands of hexadecimal values. I want to scan through the NSData and get something from every byte.

For the first half of the byte (sometimes called nibble), I want it's string value. For example : 6 becomes "6" and A becomes "a"...
For the other half, I want it's decimal value. For example : 6 gives me 6 and F gives me 15...

Any hint ?

-- Trax

----

For starters play with this:

    

    unsigned char byte = 0xff;
    unsigned char fourMostSignificantBits = byte >> 4;
    unsigned char fourLeastSignificantBits = byte & 0x0f;
    
    printf("4msb 0x%02x 4lsb 0x%02x\n", fourMostSignificantBits, fourLeastSignificantBits);
    printf("4msb %i 4lsb %i\n", fourMostSignificantBits, fourLeastSignificantBits);
    printf("4msb <%c> 4lsb <%c>\n", fourMostSignificantBits, fourLeastSignificantBits);



After you feel comfortable with these operations, you can move to pointers:

    

    unsigned char *bytes = (unsigned char *)[dataObject bytes];
    int i;
    int length = ([dataObject length] < 100) ? [dataObject length] : 100;
    for (i = 0; i < length; i++) {
    
        unsigned char fmsb = bytes[i] >> 4;    // fmsb - four most significant bits
        unsigned char flsb = bytes[i] & 0x0f;  // flsb - four least significant bits
        unsigned char fmsbASCI = fmsb + ((fmsb > 9) ? 87 : 48);
        unsigned char flsbASCI = flsb + ((flsb > 9) ? 87 : 48);
        
        NSLog(@"fmsb: hex: %x int: %2i asci: %c     flsb: hex: %x int: %2i asci: %c", fmsb, fmsb, fmsbASCI, flsb,  flsb, flsbASCI);
        
    }



This is basic bit manipulation. Any good C book should cover shifting and masking bits. When you say a string value do you mean the asci value? When you say     6 do you mean 0110?
When you say A do you mean 1010? 

----

I agree, this is a good time to use straight up c.

