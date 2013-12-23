---
layout: page
title: StringFormatTypes
---

use these with General/NSLog or your favorite routine that needs a stringWithFormat:

Format Types:

***%d, %D, %i** � Signed 32-bit integer (long)

***%u, %U** � Unsigned 32-bit integer (unsigned long)

***%hi** � Signed 16-bit integer (short)

***%hu** � Unsigned 16-bit integer (unsigned short)

***%qi** � Signed 64-bit integer (long long)

***%qu** � Unsigned 64-bit integer (unsigned long long)

***%x** � Unsigned 32-bit integer (unsigned long), printed in hexadecimal using the digits 0�9 and lowercase a�f

***%X** � Unsigned 32-bit integer (unsigned long), printed in hexadecimal using the digits 0�9 and uppercase A�F

***%o, %O** � Unsigned 32-bit integer (unsigned long), printed in octal

***%f** � 64-bit floating-point number (double)

***%e** � 64-bit floating-point number (double), printed in scientific notation using a lowercase e to introduce the exponent

***%E** � 64-bit floating-point number (double), printed in scientific notation using an uppercase E to introduce the exponent

***%g** � 64-bit floating-point number (double), printed in the style of %e if the exponent is less than �4 or greater than or equal to the precision, in the style of %f otherwise

***%G** � 64-bit floating-point number (double), printed in the style of %E if the exponent is less than �4 or greater than or equal to the precision, in the style of %f otherwise

***%c** � 8-bit unsigned character (unsigned char), printed by General/NSLog() as an ASCII character, or, if not an ASCII character, in the octal format \\ddd or the Unicode hexadecimal format \\udddd, where d is a digit

***%C** � 16-bit Unicode character (unichar), printed by General/NSLog() as an ASCII character, or, if not an ASCII character, in the octal format \\ddd or the Unicode hexadecimal format \\udddd, where d is a digit

***%s** � Null-terminated array of 8-bit unsigned characters

***%S** � Null-terminated array of 16-bit Unicode characters

***%p** � Void pointer (void *), printed in hexadecimal with the digits 0�9 and lowercase a�f, with a leading 0x. The unique address of General/AnObject.

***%@** � Objective-C object, printed as the string returned by descriptionWithLocale: if available, or description otherwise. A string.
