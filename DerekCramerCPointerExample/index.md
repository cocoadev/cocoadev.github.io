---
layout: page
title: DerekCramerCPointerExample
---

    
int add(int x)
{
    return x+1;
}

void pointerAdd(int *x)
{
     ++*x; /* the original line here, x = x+1, just incremented x, a local copy of the pointer parameter */
}

void main(void)
{
     int num1 = 0;

     printf("%d\n", num1); /*prints 0 */

     num1 = add(num1);

     printf("%d\n", num1); /*prints 1 */

     pointerAdd(&num1);

     printf("%d\n", num1); /*prints 2 */
}


----

I should point out that this code doesn't work as intended; the implementation of pointerAdd really needs to be <code>*x = *x + 1;</code>.  Your code as written will print 0 then 1 and then 1 again. --Bo

*I fixed the code (I hope :) -- KritTer*

----

Thanks, I was just trying to explain something from the chat room.  I love how fast this place works...=)

--DerekCramer

