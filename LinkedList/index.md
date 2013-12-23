---
layout: page
title: LinkedList
---



A linked list is a collection of objects or structs similar to an array. It's like opening a mailbox at a house containing a note telling you (among other things) the address of the next mailbox to look in, without the implication that the next house is right next door.

A nice example in ObjC is found at DoublyLinkedList

and see also  DesignDoublyXORLinkedList

For even more thrills and chills, see DesignMatrix

In C/C++, arrays have limited capacity, defined at compile-time. Linked lists, however, exhibit a different kind of flexibility from an array and won't give you errors if you access a non-existent object, because if you do everything correctly that's impossible.

There are lots of ways to implement linked lists or replace them. NSMutableArray and a simple C Array with some memory allocation tricks work. You could use a number system like arrays, but with objects. Maybe or maybe not. Whatever.

Insertion into a list is much more efficient than into a (mutable) array when the collection is above a certain size (YOUR EXPERT TESTIMONY HERE)

----
I'll contribute my "expert" testimony. :-) Insertion at the front of a linked list (or the back if you have a fancy list) is constant time. Insertion into the front of an expandable array is linear time. However, inserting at the end of a properly-implemented expandable array is also constant time, and so it's not any slower than a linked list. 

----
The last item in a linked list has the address NULL. Let's get down to coding - here's a C++ example.

    
#include<iostream.h>

struct paper 
 {
   int value;      // Some value (information)....
   paper *next; // Pointer to next node....
 };
paper *start_pointer = NULL;

// That is our initialization. Now for adding something to the tail of a list and some other functions:

void addTail ()
  {  paper *temp, *temp2;   // Temporary pointers

     // Reserve space for new node and fill it with data
     temp = new paper;
     cout << "Please enter a number: ";
     cin >> temp->value;
     temp->next = NULL;

     // Set up link to this node
     if (start_pointer == NULL)
         start_pointer = temp;
     else
       { temp2 = start_pointer;
         // We know this is not NULL - list not empty!
         while (temp2->next != NULL)
           {  temp2 = temp2->next;
              // Move to next link in chain
           }
         temp2->next = temp;
       }
  }

//  Delete first node in the list
void delete_start_paper()   
{ 
     paper *temp = start_pointer;
     start_pointer = start_pointer->next;
     delete temp;
}

int main(int argc, char *argv[])
{
    while(start_pointer->value != 0)
        addTail();
}



Suggestion: If you are doing a bunch of heavy lifting with lists and algorithm's, it might be worth your while to use STL linked lists, they are implemented better than you can make them, and dealing with node allocation-deallocation is error prone. Of course if you just want a simple list, feel free to write it, just don't try to reproduce the capability of mature container toolkits unless for educational reasons. My recommendation of STL is due to th fact that I am familiar with C++, I am sure there are good Objective-C list classes out there.

