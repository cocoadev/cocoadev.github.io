---
layout: page
title: GenericProgramming
---



GenericProgramming is a way to write code that does not rely on the underlying types.

For example, imagine you want to write a text editor. You may wish to write it under Mac OS X today, and thus the storage type for text should be unichar, but a port to Linux or Windows may later be handy, and here you'd use either char or wchar_t for the text.

So it would be cumbersome to have a lot of unichar types hardcoded into your source. In CPlusPlus you can abstract away the type by writing a template, which is basically a template of how the code should look. Template parameters is often the type the code should use, but it could also be an integer. Here is an example:

    
template <unsigned ibase, unsigned obase, typename _InputIter, typename _OutputIter>
_OutputIter reinterpret_base (_InputIter first, _InputIter last, _OutputIter out)
{
   for(unsigned ch = 0, hasBits = 0; first != last; ++first)
   {
      ch = (ch << ibase) | *first;
      hasBits += ibase;

      while(hasBits >= obase)
         *out++ = (ch >> (hasBits -= obase)) & ((1 << obase)-1);
   }
   return out;
}


This function can be used to convert a sequence of bytes with n bits pr. byte, to a new sequence with m bits pr. byte. For example if you wish to base64 encode a text, you must first grab the six first bits from the first byte, output this as a new byte, then take the remaining 2 bits and 4 bits from the next byte, concat these and output as the second byte and so on, this would be:

    
   char buf[120];
   ReadFile(..., buf); // assume buf now contains the file
   char newBuf[120 * 8 / 6]; // the result is larger

   reinterpret_base<8, 6>(buf, buf + sizeof(buf), newBuf);


The code above uses a static sized C array for the result, a much better solution would be:

    
vector<unsigned char> newBuf;
if(FILE *fp = fopen(file, "r"))
{
   unsigned char buf[120];
   while(size_t len = fread(resBuf, 1, sizeof(resBuf), fp))
      reinterpret_base<8, 6>(buf, buf + len, back_inserter(newBuf));
   fclose(fp);
}


Notice that nothing needs to be changed with the reinterpret_base function. Now if we want to convert the contents of newBuf back to eight bit, then we can use the following:

    
   vector<unsigned char> eight_bit;
   reinterpret_base<6, 8>(newBuf.begin(), newBuf.end(), back_inserter(eight_bit));


We can also use the function in unexpected ways, e.g. providing a binary dump of a variable:

    
   unsigned char i = 1+4+16+64;
   reinterpret_base<8, 1>(&i, &i+1, ostream_iterator<int>(cout));


This will read one bit at a time and output it, the ostream_iterator gives an output iterator which writes to stdout, so it will write the new byte, which will be 0 or 1.

