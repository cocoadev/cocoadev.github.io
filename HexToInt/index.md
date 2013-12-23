---
layout: page
title: HexToInt
---




Below is a history of the fun I had looking for a way to convert a hex string to an int value. This is how you reinvent the wheel!! The answer to my search was the stdlib library function     strtol(), this function will allow you to specify the base of the string (e.g. decimal = 10, hex = 16, oct = 8). Just do a man search in Terminal (    man strtol) for more info. I totally forgot about this function (silly me). --zootbobbalu

----


Spent about 20 minutes searching the web for a standard library function that does this and gave up because I knew I could write one in less time!! If anyone knows of a function that does this, please post here. --zootbobbalu

Is there a specific length of string you are handling?  It might be easier to do a lookup on the hex digit, then multiply by the position. Or see conversion of a decimal string code below. --BruceB 

Isn't that what this is doing? If not, could you write an example? --zootbobbalu

----

This code is faster than creating a hex string with     sprintf(buf, "%x", int_value), so it should be useful in speed critical applications. This function does not trim the input string and it doesn't account for a hex prefix     0x, I would use the sscanf() version below this example if you want an idiot proof implementation. 

    

unsigned char HEX_2_INT_TABLE[] = {0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 
            0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 
            0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 2, 3, 4, 5, 
            6, 7, 8, 9, 0, 0, 0, 0, 0, 0, 0, 10, 11, 12, 13, 14, 15, 0, 0, 
            0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 
            0, 0, 0, 10, 11, 12, 13, 14, 15, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 
            0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0};
            
int hexstr2int(char *hexstr) {
    register unsigned int length, i, value, shift;
    for (length = 0; length < 9; length++) if (!hexstr[length]) break;
    shift = (length - 1) * 4;
    for (i = value = 0; i < length; i++, shift -= 4)
        value += HEX_2_INT_TABLE[(unsigned int)hexstr[i] & 127] << shift;
    return value;
}



----

Convert a standard character string to an integer. 

This is fine for number strings, but there is already a function that does this     atoi(). My original post was for converting hex strings (e.g. 0xF20A0F34) into int values.   

The code can be easily extended to hex---look up the hex digit and multiply by 16 instead of 10.

Well the original function I posted already uses a lookup table and it shifts values instead of multiplying (a multiply is more CPU intensive). When you say "look up the hex digit" you do mean use a lookup table, or can C do this for you?

I extended the example a bit to clarify what I meant.

I see now what you mean by "look up the hex digit". Well, your example is basically the same as mine, except I avoid conditionals by using a mask and an expanded lookup table and I avoid multiplying each hex character by shifting values, but all of this is moot since     strtol() is the best way to do this. 

    
int main()
{
   int n;
   n = ConvertStrToInt( "0" );
   printf( "n: %d\n", n );
   n = ConvertStrToInt( "10" );
   printf( "n: %d\n", n );
   n = ConvertStrToInt( "124" );
   printf( "n: %d\n", n );

   nH = ConvertHexStrToLong( "000A" );
   printf( "nH: %ld\n", nH );
   nH = ConvertHexStrToLong( "000F" );
   printf( "nH: %ld\n", nH );
   nH = ConvertHexStrToLong( "00A0" );
   printf( "nH: %ld\n", nH );
   nH = ConvertHexStrToLong( "CAFE" );
   printf( "nH: %ld\n", nH );
}

int ConvertStrToInt( char* s )
{
    int  n = 0;
    char c = *s++;

    while( c >= '0' && c <= '9' )
    {
        n = n * 10 + (c - '0');    
        c = *s++;
    }

    return n;
}

long ConvertHexStrToLong( char* s )   
{
    int hexDigit[] = { 10, 11, 12, 13, 14, 15 };
    long n = 0;
    char c = *s++;

    while( c != '\0' )
    {
        if( c >= '0' && c <= '9' )
            n = n * 16 + (c - '0');
        else
        {
            c = tolower( c );
            n = n * 16 + hexDigit[(c - 'a')];
        }

        c = *s++;
    }

    return n;
}



----


It looks like someone else wrote a function that does this with the same name 

http://www-users.cs.umn.edu/~xiaobin/graphics/p2.c

    sscanf() is a flexible approach, I'll report back after I compare the two.  --zootbobbalu

    

unsigned int  hexstr2int(char* s)
{
  unsigned int n;
  int r;

  r = sscanf(s,"%x",&n);

  if (r != 1) {
    printf("error when change hex string to integer.");
    exit(0);
  }
  return n;
}



----

and of course, there are the strto* family of functions:


* strtol: long int
* strtoul: unsigned long int
* strtoll: long long int
* strtoull: unsigned long long int
* strtof: float
* strtod: double
* strtold: long double


the int versions allow you to specify the base; for hexadecimal, this is 16. (sadly, the floating-point versions do not have this feature.)

a handy feature of the strto*l family is that when the base is 0, they intelligently pick out whether the string is decimal, octal, or hex:


* "0x2a": hexadecimal (starts with 0x or 0X)
* "052": octal (starts with 0)
* "42": decimal


finally, there are atoi and atof, for int and double, respectively. these aren't as full-featured as the strto* family, though.

*--boredzo*
----

This is slightly off topic (but not worth creating a new topic for ...): Having just spent *way* too much
time looking for info on the arctangent function, I was just wondering where all of these functions are documented. Is there a single
document with all of the standard functions in it?

*--Paul*

Yes, the C standard:  http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1124.pdf *--TkM*
----

----
You can also open up     math.h and browse through all of the fun math functions provided.

