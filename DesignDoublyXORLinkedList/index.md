---
layout: page
title: DesignDoublyXORLinkedList
---

**Object Design:** *Doubly-XOR-Linked List*

----


* part1 - What a doubly-linked list is http://goo.gl/OeSCu
* part2 - The XOR optimization
* part3 - Other optimizations
* appendix - KTLLMutableArray


----**part1 - What a doubly-linked list is**----

The doubly-linked list is a common array implementation when constant insertion, access and deletion times are wanted at both beginning and end of the array. A doubly-linked list consists of lots of elements scattered throughout memory; to keep track of the order of the list, every element has pointers to the element on its left and its right (hence *doubly-linked*). Lists can also be implemented with just one of these pointers, saving a pointer's worth of memory for every element in the list but losing the constant insertion, access and deletion to one end of the list.

The array itself is stored by remembering its leftmost or *head* element, and its rightmost or *tail* element:
    
            NULL   <-------- prev   <-------- prev
 head ---> object1          object2          object3 <--- tail
            next --------->  next --------->  NULL


All the operations to search and modify a doubly-linked list are obvious; I shall not waste space by describing them here.

The cost of a doubly-linked list is three pointers per element in the list: the previous and next pointers, and a pointer to the object stored at each entry. Compare this to the single pointer per entry of a standard C array, and it looks rather expensive. Fortunately, we can optimize.

----**part2 - The XOR optimization**----

C supports the XOR bitwise operator     ^. The exact operation is unimportant to this discussion and easy to find elsewhere; we want it because of a nice property it has:     a^(a^b) == b.

In other words, if we know     prev and     prev^next, it is a simple matter to find     next. As my nomenclature may have suggested, we are going to use this in our doubly-linked list by XOR-ing together the previous and next pointers in every entry of our list. As we traverse the list, as long as we keep track of one of the current node's neighbours, we can do everything with the XOR list that we could with the original doubly-linked list design, but because two pointers XOR-ed together take no more room than one pointer, we have saved a pointer of memory per entry in the list, at the expense of more obscure code. The XOR-ed pointer value is usually labeled *pn*.

    
 head --------> object1 <--------  pn   --------> object3 <------- tail
          NULL<-  pn   --------> object2 <--------  pn   ->NULL


Since the operations to traverse a doubly-XOR-linked list are a little more complex than a simple doubly-linked list, I shall list some here. Note that this code won't actually compile verbatim as pointers cannot be XOR-ed without explicit typecasting in ObjC. Also, I have ignored niceties about how memory is allocated and freed.


* Processing along the list:
    
 next = current->pn ^ prev;
 or     
 prev = current->pn ^ next;


* Removing the head of the list:
    
 [head->object release];
 if (head->pn != NULL)
     head->pn->pn ^= head;
 head = head->pn; // since pn^NULL == pn


* Adding a new entry to the head of the list:
    
 new->object = [anObject retain];
 if (head != NULL)
     head->pn ^= new;
 new->pn = head;
 head = new;


* Reversing the order of the list:
    
 temp = head;
 head = tail;
 tail = temp;
 or (saving a temporary pointer)
    
 head ^= tail;   // Now have head == a^b, tail == b
 tail ^= head;   // Now have head == a^b, tail == b^a^b == a
 head ^= tail;   // Now have head == a^b^a == b, tail == a



Note that this last relies upon a symmetry the doubly-XOR-linked list has that the plain doubly-linked list does not: that the direction you process along the list depends upon what you set your previous pointer to be. If     current == tail and     prev == NULL, you have just started processing in reverse along the array. If     current == tail and     prev == current->pn, you have just finished traversing the array. Thus swapping the head and tail pointers swaps the direction of the array.

Since the doubly-XOR-linked list is concerned with optimizing memory usage, there are other optimizations that go well with it.

----**part3 - Other optimizations**----

Allocating chunks of memory is costly in time and wasted memory. Allocating large swathes at a time is much better than tiny bites, and the two pointers per entry of a doubly-XOR-linked list definitely counts as tiny. It is best, therefore, to allocate a large chunk of memory and keep track of what is used and what not. We do this by keeping a singly-linked list of unused nodes (since we'll only ever want to add or remove entries to/from the head of the list). This allows us to keep head/tail insertions and deletions constant-time, except in the case when we run out of spare room in the array.

Since we are keeping our list in a large, contiguous chunk of memory, we may as well use array indexing instead of pointers inside the array. This has two benefits. Firstly, unsigned indexes can be XOR-ed together without typecasting, making much neater code. Secondly, the easiest way to resize the list if it gets too full is with NSZoneRealloc (see NSZone), which will duplicate the memory byte-for-byte elsewhere if necessary. If we used pointers, we would have to go through the array and change every pointer in it if this duplication occurred because none of the pointers would be valid any more; if we use indexes, they will stay valid if the array is duplicated, saving us a lot of effort.

These two optimizations ensure our memory usage is two pointers per entry plus a constant amount (cf plus an unknown amount per entry), and that head/tail insertion/deletion is kept constant-time.

----**appendix - KTLLMutableArray**----

This design, with both the extra optimizations mentioned, is implemented in an open source NSMutableArray subclass; see KTLLMutableArray.

-- KritTer, comments and corrections welcome

----
*The doubly-linked list is a common array implementation when constant insertion, access and deletion times are wanted at both beginning and end of the array*

For this we actually have the DeQue -- the advantage of a double linked list is constant time removal and insertion at *any* place in the list (including constant time list splicing or splitting), which the XOR trick does not allow us.

--AllanOdgaard
----
I should go on to say the XOR hack of swapping two values w/o using a temporary placeholder will cause the processor pipeline to stall. Time critical code should stick with the less-geeky technique of using a temporary pointer. Never mind code safety issues of someone accidentally dereferencing a mangled pointer.

-- MikeTrent
----
Why would it cause the pipeline to stall?

----
You can also use addition and subtraction instead of XORing:
    
 a=10;
 b=-2;
 a-=b; // a == 12
 b+=a; // b == 10
 a=b-a; // a == -2

I guess it doesn't look as cool. But it's almost as obfuscated.

*Actually, you can use any operation where you can get an original value back given the other original operand. This also includes multiplication/division. But I would guess XOR is often used because it is more efficient than subtraction or division (or at least was in the past), and "looks neater" because it is contained in a single operation. Personally, I always found this more confusing. Another advantage is that it cannot possibly exceed storage limitations since all operations are being done to a single bit. --JediKnil*
    
 // a and b are integers in the expressions
 // c is either a or b, and d is the other one
 // But you don't know which.
 int xorExp = a ^ b; // d == (xorExp ^ c)
 int sum = a + b; // d == (sum - c)
 int product = a * b; // d == (product / c)
 
 // Can you figure out why these won't work?
 int andExp = a & b; // d != (andExp & c)
 int orExp = a | b; // d != !(orExp | c)
 
 // And these? (different reason)
 int difference = a - b; // d != (difference + c)
 float fraction = (float) a / b // d != (fraction * c)
 
 // Would you even expect this to work?
 int modExp = a % b; // ?


Multiplication/division will work for this purpose in the theoretical, mathematical sense (they are reversible mathematical operations), but they won't work in the real world unless you use a 64-bit integer with 32-bit pointers (or more generally, if you use twice as much storage for the result as you use for a pointer), which completely defeats the purpose of this crazy fun anyway. When using standard int or float types, it is not always true that     a / b * b == a or that     a * b / b == a. -- PrimeOperator

