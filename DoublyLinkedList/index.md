---
layout: page
title: DoublyLinkedList
---



The following is the actual Objective C class that implements a doubly-linked list (a variation of LinkedList). It can do the following:


*Addition of new elements to the front of the list
*Forward traversal using internal iterator
*Backward traversal
*Removal of the item the iterator points at


Here's the interface .h file:

    
//
//  LinkedList.h
//

// Structure representing a 
// doubly-linked list node.
typedef struct ListNode ListNode;
struct ListNode {
	int value;
	ListNode *next;
	ListNode *prev;
};


@interface LinkedList : NSObject {
@private 
	ListNode *head;
	ListNode *iterator;
	//bool reachedHead;
	//bool reachedTail;
}	

- (id)initWithHead: (int)value;
- (void)addToFront: (int)value;
- (int)getFirst;
- (int)getCurrent;
- (int)getNext;
- (int)getPrevious;

- (bool)atHead;
- (bool)atTail;

- (int)removeCurrent;

@end


Now for the implementation .m file:

    
//
//  LinkedList.m
//

#import "LinkedList.h"


@implementation LinkedList


/* Instantiates new linked list with a 
 * given first element. 
 */
- (id)initWithHead: (int)value 
{
	ListNode *n;
    self = [super init];
    if (self) {
		// creating head node with given value
		n = (ListNode *)malloc(sizeof(ListNode));
		n->value = value;
		n->next = NULL;
		n->prev = NULL;
		head = n;
		// initializing iterator to default
		[self getFirst];
    }
    return self;	
}


/* Adds a new element to the
 * front of the list */
- (void)addToFront: (int)value
{
	ListNode *n = (ListNode *)malloc(sizeof(ListNode));
	n->value = value;
	n->next = head;
	n->prev = NULL;
	// new element becomes the head node
	head->prev = n;
	head = n;
}


/* Sets internal iterator to
 * the head node and returns its
 * value */
- (int)getFirst {
	iterator = head;
	//reachedHead = TRUE;
	//reachedTail = FALSE;
	return (head->value);
}

/* Returns the value of the iterator node
 */
- (int)getCurrent {
	return (iterator->value);
}


/* Iterates to the next node in order and
 * returns its value */
/*
- (int)getNext
{
	// if we are finished iterating,
	// set the end-of-list flag
	if (iterator->next == NULL) {
		reachedTail = TRUE;
	} else {
		// if we're leaving the head
		// node, set the flag
		if (iterator->prev == NULL) {
			reachedHead = FALSE;
		}
		iterator = iterator->next;
	}
	return (iterator->value);
}
*/
- (int)getNext
{
     if (iterator->next != NULL) {
          iterator = iterator->next;
     }
     return (iterator->value);
}


/* Iterates to the previous node in 
 * order and returns its value */
/*
- (int)getPrevious
{
	if (iterator->prev == NULL) {
		reachedHead = TRUE;
	} else {
		if (iterator->next == NULL) {
			reachedTail = FALSE;
		}
		iterator = iterator->prev;
	}
	return (iterator->value);
}
*/
- (int)getPrevious
{
     if (iterator->prev != NULL) {
          iterator = iterator->prev;
     }
     return (iterator->value);
}
	

/* Indicates that iterator
 * is at the first (head) node */
- (bool)atHead 
{
	//return reachedHead;
     return (iterator->prev == NULL);
}


/* Indicates that iterator
 * is at the last (tail) node */
- (bool)atTail 
{
	//return reachedTail;
     return (iterator->next == NULL);
}


/* Removes the iterator node from
 * the list and advances iterator to the
 * next element. If there's no next element,
 * then it backs iterator up to the previous
 * element. Returns the old iterator value */
- (int)removeCurrent 
{
	int i = iterator->value;
	ListNode *l;
	// if we have only 1 item in the list...
	if ((iterator->next == NULL) && (iterator->prev == NULL)) {
		//... then we can safely delete it and set head to null
		free(iterator);
		iterator = NULL;
		head = NULL;
	} else {
		// sawing the gap between nodes
		l = iterator;
		if (iterator->next != NULL) {
			iterator->next->prev = iterator->prev;
		}
		if (iterator->prev != NULL) {
			iterator->prev->next = iterator->next;
		}

                // if removing node is head, set head.
                if(iterator == head) {
                        head = iterator->next;
                }

		// finally setting new iterator
		iterator = (iterator->next != NULL) ? iterator->next : iterator->prev;
		free(l);
	}
	// returning old value
	return i;
}

@end


So, essentially this is it. I assume the basic knowledge of how linked list operates. You can just plug in this class and use it in your programs, although I think several things need to be added for comfortable operation, like element search e.t.c.
You can test this class with a test sniplet like the following:

    
#import <Foundation/Foundation.h>
#import "LinkedList.h"

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
	
    // insert code here...
	LinkedList *test = [[LinkedList alloc] initWithHead: 0];
	
	// testing addition
	int i;
	for (i = 1; i < 11; i++) {
		[test addToFront: i];
	}
	NSLog(@"-- Added 10 values");
	
	// testing forward traversal
	NSLog(@"-- Setting iterator to first node: %d", [test getFirst]);
	NSLog(@"-- Going forward until we hit the last element...");
	do {
		NSLog(@"---- Next element: %d", [test getNext]);
	} while ([test atTail] == FALSE);
	NSLog(@"-- We've hit the last element");
	
	// testing back traversal
	NSLog(@"-- Going back until we hit the first element...");
	do {
		NSLog(@"---- Previous element: %d", [test getPrevious]);
	} while ([test atHead] == FALSE);
	NSLog(@"-- We've hit the first element");

	// testing removal
	NSLog(@"-- Going forward 5 times");
	for (i = 0; i < 5; i++)
		NSLog(@"-- Next element: %d", [test getNext]);
	NSLog(@"-- Current element with value %d", [test getCurrent]);
	NSLog(@"-- Removing element with value %d", [test removeCurrent]);
	NSLog(@"-- Continue forward traversal");
	do {
		NSLog(@"---- Next element: %d", [test getNext]);
	} while ([test atTail] == FALSE);
	NSLog(@"-- We've hit the last element");

	NSLog(@"-- Returning to front, going forward until we reach end");
	NSLog(@"-- First element: %d", [test getFirst]);
	do {
		NSLog(@"---- Next element: %d", [test getNext]);
	} while ([test atTail] == FALSE);
	NSLog(@"-- We've hit the last element");
	
    [pool release];
    return 0;
}



One thing that still bugs me is that when you traverse back in this test program, the last traversal element gets printed twice, I guess because of the way my flag works. 

--DimitriT

----

Wonderful object Dimitri. You'd actually be able to use it in Cocoa, which is good. 
A note about my comment earlier (I'm editing that part) it WILL work in C, but you'll have to replace the cin and cout objects with printf() and gets()... 
I'd rather not mess with that though, because of buffer errors or whatever happens with those to....
Yeah, I started with C++... not a smart move V_V...
Anyone have any ideas on how to implement multidimensional lists? 
Final note:
Linked lists are GREAT for fetching command-line arguments. GREAT....
Then, you could implement an array of strings with a sizeof(whatever*argc) or something...
But that wouldn't be fun, object-oriented or cool. Or obfuscated.

----
Command line arguments? You are kidding... right?
----
Although, If C++ is being used, I would just use the stl std::list and forego defining my own container. ;-)

----
Shh! They aren't supposed to know about that!!!

----
This is cool. I've made some code edits that correct the issue of the last traveral element being accessed twice -- fundamentally, the issue of course is that the boundary flag didn't get updated until after the next iteration/check had already taken place. Initially I modified getPrevious and getNext to make sure to update the flags before returning, but then I realized maybe everythings just simpler if we do away with maintaining flags, and simply evaluate our boundaries in place? Makes the code simpler anyway. Thoughts? (I just commented out the parts I changed for now).
Another question -- is it better for getPrevious and getNext to keep returning the terminal value if the iterator is already at the boundary, or should it return NULL?
--WardR

----
This is pretty cool, but if you change the way you detect the end/head to the way they do it on the STL library you could get a huge performance improvement when doing a lot of operations (i.e. particle systems). Instead of having the head and end pointing to nulls, create a NULL node and make it a circular list. That way you get rid of a lot of if statements.

darionco

----
I used this code for my game. (with changing int -> NSObject*, could contain all kind of classes)
It worked well, until remove nodes.
I found that after remove first node, 'head' is unavailable so next 'getFirst' call causes problem.

I added codes
    if(iterator == head) {
            head = iterator->next;
    }
Now it works well!
(spent 3 hours for this o_O)

minsumansu

