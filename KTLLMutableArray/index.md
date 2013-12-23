---
layout: page
title: KTLLMutableArray
---

**KTLLMutableArray**
----

This code creates a subclass of NSMutableArray implementing DesignDoublyXORLinkedList for constant-time head/tail operations, at the cost of two pointers per array element (cf one for simple C-array implementations) and O(n) times for general operations.

Aside from reverseObjects, which reverses the order of the array, this subclass has no methods not found in NSMutableArray, so no documentation is provided. http://goo.gl/OeSCu

Note that using an enumerator is the fastest way of traversing all the objects in this class, unlike the standard NSArray implementations.

Feel free to correct errors in this code, add comments, etc. (Any changes you make to the code on this page, however, will be placed under the license described below.) This code *has* been tested, but not exhaustively, so bugs may still lurk.

-- KritTer

----

*Are you sure this is even necessary?  The standard NSMutableArray implementation is analogous to the STL deque (*d*ouble-*e*nded *que*ue), where head and tail operations are constant-time due to the beginning of the array being somewhere in the middle of an allocated chunk of memory*

Think of this as an intellectual exercise. Specifically, exercise 11.2-8 of Cormen, Leiserson and Rivest's "Introduction to algorithms". The interesting feature is the O(1)     reverseObjects method.

----

    
 //
 // KTLLMutableArray
 // Doubly-XOR-Linked List NSArray subclass
 //
 // The XOR Doubly Linked List takes twice the room of a C-array-using
 // NSArray subclass to allow very fast head/tail insertion and deletion
 // Head/tail access/insertion/deletion is O(1), but these operations are O(n)
 // in general
 // Reversing the list is an O(1) operation
 //
 // Copyright (c) 2002 Chris Purcell.
 //
 // Permission is hereby granted, free of charge, to any person obtaining a
 // copy of this software and associated documentation files (the "Software"),
 // to deal in the Software without restriction, including without limitation
 // the rights to use, copy, modify, merge, publish, distribute, sublicense,
 // and/or sell copies of the Software, and to permit persons to whom the
 // Software is furnished to do so, subject to the following conditions:
 //
 // The above copyright notice and this permission notice shall be included
 // in all copies or substantial portions of the Software.
 //
 // THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 // IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 // FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL
 // THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 // LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
 // FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
 // DEALINGS IN THE SOFTWARE.
 //
 
 #import <Foundation/Foundation.h>
 
 /***************************************************************************'/
 /'                        KTLLMutableArray interface                        '/
 /'***************************************************************************/
 @interface KTLLMutableArray : NSMutableArray
 {   // Note: offsets range [1, capacity], not [0, capacity)
     // 0 offset means "no such object"
   void *memory;
   unsigned capacity;       // Size of memory
   unsigned uninitialized;  // First uninitialized offset
   unsigned count;          // Number of objects stored
   unsigned head;           // Offset of first used node
   unsigned tail;           // Offset of last used node
   unsigned unused_head;    // Offset of first empty node (singly linked)
 }
 
 - (void)reverseObjects;
 
 @end
 
 
 /***************************************************************************'/
 /'                            Element structure                             '/
 /'***************************************************************************/
 struct element
 {
   id object;
   unsigned pn; // prev and next indices XOR-ed together
 } ;
 
 /***************************************************************************'/
 /'                            Object enumerator                             '/
 /'***************************************************************************/
 @interface KTLLMutableArrayEnumerator : NSEnumerator
 {
   struct element *array;
   unsigned offset, prev;
   id collection;
 }
 
 // initWithArray:firstOffset:forCollection:
 - (id)initWithArray:(struct element *)_ar
 firstOffset:(unsigned)_off
 forCollection:(id)_co;
 @end
 
 @implementation KTLLMutableArrayEnumerator
 
 - (id)initWithArray:(struct element *)_ar
 firstOffset:(unsigned)_off
 forCollection:(id)_co
 {
   if ((self = [super init]))
   {
     array      = _ar;
     offset     = _off;
     prev       = 0;
     collection = [_co retain];
   }
   return self;
 }
 
 - (id)nextObject
 {
   id ret;
   if (offset == 0)
     return nil;
   else
     ret = array[offset-1].object;
     prev   ^= offset^array[offset-1].pn;
     offset ^= prev;
     prev   ^= offset;
     return ret;
 }
 
 - (void)dealloc
 {
   [collection release];
   [super dealloc];
 }
 @end
 
 /***************************************************************************'/
 /'                     KTLLMutableArray implementation                      '/
 /'***************************************************************************/
 @implementation KTLLMutableArray
 
 - (id)initWithObjectList:(id)firstObj :(va_list *)toCount :(va_list *)args
 {
   if ((self = [super init]))
   {
     unsigned i;
     struct element *array;
     
     do
     {
       count++;
     }
     while (va_arg(*toCount, id));
     va_end(*toCount);
     
     head = 1;
     tail = capacity = count;
     unused_head = 0;
     uninitialized = capacity + 1;
     
     memory = NSZoneCalloc([self zone],
                               capacity,
                               sizeof(struct element));
     array = memory;
     array[0].object = [firstObj retain];
     array[0].pn     = 2;
     for (i = 2; i <= count; i++)
     {
       array[i-1].object = [va_arg(*args, id) retain];
       array[i-1].pn     = (i-1)^(i+1);
     }
     array[count-1].pn = count-1;
     va_end(*args);
   }
   return self;
 }
 
 + (id)array
 {
   return [[[self alloc] init] autorelease];
 }
 + (id)arrayWithCapacity:(unsigned)numItems
 {
   return [[[self alloc] initWithCapacity:numItems] autorelease];
 }
 + (id)arrayWithArray:(NSArray *)anArray
 {
   return [[[self alloc] initWithArray:anArray] autorelease];
 }
 + (id)arrayWithObject:(id)anObject
 {
   return [[[self alloc] initWithObjects:&anObject count:1] autorelease];
 }
 + (id)arrayWithObjects:(id)firstObj, ...
 {
   if (firstObj)
   {
     va_list args1, args2;
     va_start(args1, firstObj);
     va_start(args2, firstObj);
     return [[[self alloc] initWithObjectList:firstObj :&args1 :&args2]
             autorelease];
   }
   else
     return [[[self alloc] init] autorelease];
 }
 + (id)arrayWithObjects:(id *)objects count:(unsigned)objectCount
 {
   return [[[self alloc] initWithObjects:objects
                                   count:objectCount] autorelease];
 }
 - (id)init
 {
   if ((self = [super init]))
   {
     memory = NULL;
     count = capacity = head = tail = unused_head = 0;
     uninitialized = 1;
   }
   return self;
 }
 - (id)initWithCapacity:(unsigned)numItems
 {
   if ((self = [super init]))
   {
     capacity = numItems;
     uninitialized = 1;
     head = tail = unused_head = count = 0;
     
     if (capacity == 0)
       memory = NULL;
       else
         memory = NSZoneCalloc([self zone],
                                   capacity,
                                   sizeof(struct element));
         }
   return self;
 }
 - (id)initWithArray:(NSArray *)anArray
 {
   if ((self = [super init]))
   {
     unsigned i;
     NSEnumerator *j = [anArray objectEnumerator];
     struct element *array;
     
     head = 1;
     tail = count = capacity = [anArray count];
     uninitialized = capacity + 1;
     unused_head = 0;
     
     if (capacity == 0)
       memory = NULL;
       else
         memory = NSZoneCalloc([self zone],
                                   capacity,
                                   sizeof(struct element));
         array = memory;
         for (i = 1; i <= count; i++)
         {
           array[i-1].object = [[j nextObject] retain];
           array[i-1].pn     = (i-1)^(i+1);
         }
     array[count-1].pn = count-1;
   }
   return self;
 }
 - (id)initWithObjects:(id)firstObj, ...
 {
   if ((self = [super init]))
   {
     unsigned i;
     va_list args;
     struct element *array;
     
     head = unused_head = tail = count = capacity = 0;
     memory = NULL;
     
     if (firstObj)
     {
       va_start(args, firstObj);
       do
       {
         count++;
       }
       while (va_arg(args, id));
       va_end(args);
       
       head = 1;
       tail = capacity = count;
       
       memory = NSZoneCalloc([self zone],
                                 capacity,
                                 sizeof(struct element));
       array = memory;
       va_start(args, firstObj);
       array[0].object = [firstObj retain];
       array[0].pn     = 2;
       for (i = 2; i <= count; i++)
       {
         array[i-1].object = [va_arg(args, id) retain];
         array[i-1].pn     = (i-1)^(i+1);
       }
       array[count-1].pn = count-1;
       va_end(args);
     }
     uninitialized = capacity + 1;
   }
   return self;
 }
 - (id)initWithObjects:(id *)objects count:(unsigned)objectCount
 {
   if ((self = [super init]))
   {
     unsigned i;
     struct element *array;
     
     head = 1;
     tail = count = capacity = objectCount;
     uninitialized = capacity + 1;
     unused_head = 0;
     
     if (capacity == 0)
       memory = NULL;
       else
         memory = NSZoneCalloc([self zone],
                                   capacity,
                                   sizeof(struct element));
         array = memory;
         
         for (i = 1; i <= count; i++)
         {
           array[i-1].object = [objects[i-1] retain];
           array[i-1].pn     = (i-1)^(i+1);
         }
     array[count-1].pn = count-1;
   }
   return self;
 }
 
 // Querying the array
 - (unsigned)count
 {
   return count;
 }
 - (id)objectAtIndex:(unsigned)index
 {
   struct element *array = memory;
   unsigned i, prev = 0, rem = index;
   if (index*2 <= count)
     i = head;   // Start at head of array
     else if (index < count)
     {   // Start at tail of array
       i = tail;
       rem = count - index - 1;
     }
     else
       [NSException raise:NSRangeException
                       format:@"Index (%d) overflows array bounds (%d)",
        index, count];
   for (; rem > 0; rem--)
   {                        // prev = p,   i = q; i.pn = p^r
     prev ^= i^array[i-1].pn; // prev = q^r, i = q
     i    ^= prev;            // prev = q^r, i = r
     prev ^= i;               // prev = q,   i = r
   }
   return array[i-1].object;
 }
 - (NSEnumerator *)objectEnumerator
 {
   return [[[KTLLMutableArrayEnumerator alloc] initWithArray:memory
                                                     firstOffset:head
                                                   forCollection:self] autorelease];
 }
 - (NSEnumerator *)reverseObjectEnumerator
 {
   return [[[KTLLMutableArrayEnumerator alloc] initWithArray:memory
                                                     firstOffset:tail
                                                   forCollection:self] autorelease];
 }
 - (id)lastObject
 {
   if (tail == 0)
     return nil;
   else
     return ((struct element *)memory)[tail-1].object;
 }
 
 // Working with array elements
 - (void)exchangeObjectAtIndex:(unsigned)idx1
 withObjectAtIndex:(unsigned)idx2
 {
   id temp;
   struct element *array = memory;
   unsigned i, j, prev, rem;
   
   // Find object at idx1
   prev = 0;
   if (idx1*2 <= count)
   {   // Start at head of array
     i   = head;
     rem = idx1;
   }
   else if (idx1 < count)
   {   // Start at tail of array
     i   = tail;
     rem = count - idx1 - 1;
   }
   else
     [NSException raise:NSRangeException
                     format:@"Index (%d) overflows array bounds (%d)",
      idx1, count];
   for (; rem > 0; rem--)
   {
     prev ^= i^array[i-1].pn;
     i    ^= prev;
     prev ^= i;
   }
   
   // Find object at idx2
   prev = 0;
   if (idx2*2 <= count)
   {   // Start at head of array
     j   = head;
     rem = idx2;
   }
   else if (idx2 < count)
   {   // Start at tail of array
     j   = tail;
     rem = count - idx2 - 1;
   }
   else
     [NSException raise:NSRangeException
                     format:@"Index (%d) overflows array bounds (%d)",
      idx2, count];
   for (; rem > 0; rem--)
   {
     prev ^= j^array[j-1].pn;
     j    ^= prev;
     prev ^= j;
   }
   
   // Swap the objects
   temp              = array[j-1].object;
   array[j-1].object = array[i-1].object;
   array[i-1].object = temp;
 }
 
 // Adding and replacing objects
 - (void)addObject:(id)anObject
 {
   struct element *array = memory;
   unsigned i;
   if (!anObject)
     [NSException raise:NSInvalidArgumentException
                     format:@"nil object passed to array"];
   if (count == capacity)
   {   // Increase the size of the array by at least a half
     capacity = (capacity * 3) / 2 + 1;
     array = memory = NSZoneRealloc([self zone],
                                        memory,
                                        capacity * sizeof(struct element));
   }
   if (unused_head != 0)
   {   // Use the top of the unused list
     i           =  unused_head;
     unused_head =  array[i-1].pn;
   }
   else
   {   // Use uninitialized memory
     i = uninitialized++;
   }
   // Fill in details of inserted entry
   array[i-1].object =  [anObject retain];
   array[i-1].pn     =  tail;
   count++;
   // Insert entry into the list
   if (tail != 0)
     array[tail-1].pn ^= i;
     if (head == 0)
       head = i;
       tail = i;
       }
 - (void)insertObject:(id)anObject atIndex:(unsigned)index
 {   // Find somewhere to put the entry
   struct element *array = memory;
   unsigned next, prev = 0, rem, ins;
   if (index*2 <= count)
   {   // Start at head
     next = head;
     rem  = index;
   }
   else if (index < count)
   {
     next = tail;
     rem  = count - index - 1;
   }
   else if (index == count)
   {
     next = 0;
     prev = tail;
     rem  = 0;
   }
   else
     [NSException raise:NSRangeException
                     format:@"Index (%d) overflows array bounds (%d)",
      index, count];
   for (; rem > 0; rem--)
   {
     prev ^= next^(array[next-1].pn);
     next ^= prev;
     prev ^= next;
   }
   if ((index*2 > count) && (index < count))
     prev ^= array[next-1].pn;
     // Ensure prev is *before* next in the array
     
     if (!anObject)
       [NSException raise:NSInvalidArgumentException
                       format:@"nil object passed to array"];
   if (count == capacity)
   {   // Increase the size of the array by at least a half
     capacity = (capacity * 3) / 2 + 1;
     array = memory = NSZoneRealloc([self zone],
                                        memory,
                                        capacity * sizeof(struct element));
   }
   if (unused_head != 0)
   {   // Use the top of the unused list
     ins         = unused_head;
     unused_head = array[ins-1].pn;
   }
   else
   {   // Use uninitialized memory
     ins = uninitialized++;
   }
   // Fill in details of inserted entry
   array[ins-1].object = [anObject retain];
   array[ins-1].pn     = prev ^ next;
   count++;
   // Insert entry into the list
   if (prev != 0)
     array[prev-1].pn ^= next ^ ins; // Swap next for ins in prev's pn
     else
       head = ins;                     // Place ins at head of the array
       if (next != 0)
         array[next-1].pn ^= prev ^ ins; // Swap prev for ins in next's pn
         else
           tail = ins;                     // Place ins at tail of the array
           }
 - (void)replaceObjectAtIndex:(unsigned)index
 withObject:(id)anObject
 {   // Find entry to replace
   struct element *array = memory;
   unsigned i, prev = 0, rem;
   if (index*2 <= count)
   {   // Start at head
     i   = head;
     rem = index;
   }
   else if (index < count)
   {
     i   = tail;
     rem = count - index - 1;
   }
   else
     [NSException raise:NSRangeException
                     format:@"Index (%d) overflows array bounds (%d)",
      index, count];
   for (; rem > 0; rem--)
   {
     prev ^= i^array[i-1].pn;
     i    ^= prev;
     prev ^= i;
   }
   
   // Retain new object before releasing old one in case they are the same
   [anObject retain];
   [array[i-1].object release];
   array[i-1].object = anObject;
 }
 - (void)setArray:(NSArray *)otherArray
 {
   struct element *array = memory;
   unsigned i = head, prev = 0;
   NSEnumerator *j = [otherArray objectEnumerator];
   
   while (i != 0)
   {
     [array[i-1].object release];
     prev ^= i^array[i-1].pn;
     i    ^= prev;
     prev ^= i;
   }
   NSZoneFree([self zone], memory);
   
   head = 1;
   tail = count = capacity = [otherArray count];
   uninitialized = capacity + 1;
   unused_head = 0;
   
   if (capacity == 0)
     memory = NULL;
     else
       memory = NSZoneCalloc([self zone],
                                 capacity,
                                 sizeof(struct element));
       array = memory;
       for (i = 1; i <= count; i++)
       {
         array[i-1].object = [[j nextObject] retain];
         array[i-1].pn     = (i-1)^(i+1);
       }
   array[count-1].pn = count-1;
 }
 
 // Removing objects
 - (void)removeAllObjects
 {
   struct element *array = memory;
   unsigned i = head, prev = 0;
   
   while (i != 0)
   {
     [array[i-1].object release];
     prev ^= i^array[i-1].pn;
     i    ^= prev;
     prev ^= i;
   }
   
   unused_head = head = tail = count = 0;
   uninitialized = 1;
 }
 - (void)removeLastObject
 {
   if (tail != 0)
   {   // Remove the tail
     struct element *array = memory;
     unsigned i    = tail;
     
     // Move the tail of the list
     tail             =  array[i-1].pn;
     if (tail == 0)
       head = 0;
       else
         array[tail-1].pn ^= i;
         
         // Add the old tail to the unused list and release its object
         array[i-1].pn = unused_head;
         unused_head   = i;
         [array[i-1].object release];
     count--;
   }
   else
     [NSException raise:NSRangeException
                     format:@"Cannot remove objects from an empty array"];
 }
 - (void)removeObjectAtIndex:(unsigned)index
 {   // Find entry to remove
   struct element *array = memory;
   unsigned i, prev = 0, next, rem;
   if (index*2 <= count)
   {   // Start at head
     i   = head;
     rem = index;
   }
   else if (index < count)
   {
     i   = tail;
     rem = count - index - 1;
   }
   else
     [NSException raise:NSRangeException
                     format:@"Index (%d) overflows array bounds (%d)",
      index, count];
   for (; rem > 0; rem--)
   {
     prev ^= i^array[i-1].pn;
     i    ^= prev;
     prev ^= i;
   }
   
   // Remove entry from the list
   next = prev^array[i-1].pn;
   if (head == i)
     head = array[i-1].pn;
     if (tail == i)
       tail = array[i-1].pn;
       if (prev != 0)
         array[prev-1].pn ^= i^next;
         if (next != 0)
           array[next-1].pn ^= i^prev;
           // Add entry to the unused list and release its object
           array[i-1].pn = unused_head;
           unused_head   = i;
           [array[i-1].object release];
   count--;
 }
 
 - (void)reverseObjects
 {
   head ^= tail;
   tail ^= head;
   head ^= tail;
 }
 
 - (void)dealloc
 {
   struct element *array = memory;
   unsigned i = head, prev = 0;
   while (i != 0)
   {
     [array[i-1].object release];
     prev ^= i^array[i-1].pn;
     i    ^= prev;
     prev ^= i;
   }
   NSZoneFree([self zone], memory);
   [super dealloc];
   
 }
 @end


----

Just started playing with this today. I know that this has been around for a couple of years, but I never had the need for a made from scratch mutable array. I haven't done any performance tests on this yet, but from what I can see, this should be a nice starting point. I did find a memory bug though. The "memory" for the array was released in the     dealloc instance method, but the instance wasn't. I subclassed this and couldn't figure out why my subclass wasn't being deallocated when the subclass's     dealloc method was being called. I guess the moral of the story is, when you can't find a memory leak, check to make sure the parent class is doing the right thing. 

-- zootbobbalu

