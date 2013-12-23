---
layout: page
title: DesignRedBlackTree
---

**Object Design:** *Red-black tree*

----


* part1 - What a Binary Search is
* part2 - What a Binary Tree is
* part3 - What a Red-Black Tree is
* part4 - Insertions
* part5 - Deletions
* part6 - Where Red-Black trees are (and are not) used
* appendix - References and Code
* comments


----**part1 - What a Binary Search is**----

Imagine we have a set of ordered data - that is, items with an ordering such that any two are either less than, greater than or equal to each other. A good example is some set of integers, say {0, 5, 8, 1, 13}.

A binary search takes an ordered array of such data - one with the ordered data increasing in value as you go to the right, such as (0, 1, 5, 8, 13) - and locates a particular item in any given segment by comparing it to the middle item of the segment. If the item is less than the middle one, the search continues to the left; if it is greater, to the right; if it is equal, the search stops. If the interval is whittled down to zero size, the search concludes the item is not there.

For instance, to search (0, 1, **5**, 8, 13) for 1, we would first look at the middle of the whole set, 5. Since 1 < 5, we look at the numbers to the left, (**0**, 1), with 0 at the middle. Since 1 > 0, we look to the right, and find 1 there. This algorithm is o(log(n)).

----**part2 - What a Binary Tree is**----

A problem is how to keep this efficient search method for mutable sets. If we use an array, the natural first idea, we must move every object to the right of each item we insert to keep the ordering, and this is clearly unacceptable, taking o(n) time.

Instead, we note that each stage of the search only involves "the middle number", "numbers to the left", and "numbers to the right"; so we make a data structure that reflects this. With every item, we associate up to two pointers, *left* and *right*. We call a item and its pointers a *node*. The nodes these pointers reference, if any, are called the *children* of the node. We insist: 
* Every node has at most one *parent*, one node that points to it.
* Only one node has no parent, and we call this the *top* or *root* node. From this node we can reach all other nodes by following the left and right pointers.
* For any node N, any item that can be reached by following its left pointer must be less than N's item in the ordering; similarly, any node to the right must be greater.


We call this structure a **binary tree**

This allows us to replicate the efficiency of the binary sort; consider the following binary tree:     
  **5** 
 **/** \ 
**0**   8 
 **\**   \ 
  **1**   13 
 If we start at the top and look for 1, we examine exactly the same items by going left if 1 is less than the current node, right if it is greater, and stopping if we find the node, as we did in the binary search; the same is true whatever number we search for. Such a tree, which has o(log(n)) efficiency in searching, is called *balanced*.

Alas, this is not the end of the search, as the same data could have been stored in this perfectly valid tree:     
0 
 \ 
  1 
   \ 
    5 
     \ 
      8 
       \ 
        13 
 In this tree, searching for 13 takes 5 comparisons, not 3; a tree that is badly formed can take o(n) time to search. This is as bad as if we didn't bother with the tree at all!

----**part3 - What a Red-Black Tree is**----

We call a node with no children a *leaf*. The problem we have is that leaves can be very far away from the root node, while in the balanced case the distance to the furthest leaf is o(log(n)).

Balancing a tree fully - making it exactly as efficient as the binary search - involves ensuring every leaf is as close to the node as possible, and is generally a complex task. However, it is good enough just to ensure the leaves are fairly well balanced - say, not more than twice as far from the root as any other. This is what a red-black tree does.

For a red-black tree, we must add to each node a color, red or black, we ensure every node can quickly find its parent (add a pointer to it), and we must insist upon the following in addition to standard binary tree rules: 
* There is always the same number of black nodes between a leaf and the root.
* No red node ever has another red node as a child.
* The root node of the tree must always be black
 Together, these give what we desire.

Alas, that is the easy bit. The hard bit is actually writing a tree that implements this.

----**part4 - Insertions**----

To insert an item into a standard binary tree, one merely follows the tree with the standard search algorithm, then adds the new node to the left or the right of the last node we encounter. We need to do more work for a red-black tree; we need to re-balance the tree. Fortunately, this does not involve too much work.

We want to inspect several nodes in the tree, to see if that little segment needs re-balancing. There are only a few possible layouts we can encounter, and each can be resolved in a set way. We start by coloring our new node red and looking at the tree near it:

    
  **B** 
 / 
A 
 If we are inspecting A here, we need do no more work as the tree is balanced. Hurrah!

    
    **C**     >       C 
   / \    >      / \ 
  B   D   >     **B**   **D** 
 /        >    / 
A         >   A 
 If we are inspecting A here, we need only re-color the tree. However, coloring C red may have created problems further up the tree, so we need to repeat this process there. If that node is the root node, just color it black again and stop.

    
    **C**          **C**     >     **B** 
   / \        / \    >    / \ 
  B   **D**  or  A   **D**   >   A   C 
 /            \      >        \ 
A              B     >         **D** 
 If we are inspecting the bottom-most of either of these two situations, we need to actually re-balance the tree as shown. We also need to be sure the children of each node are kept in the correct relative places - B's right child must become C's left node in both, while in the second situation we also need to set B's left node as A's right. Finally, D may be NULL (as in, C may not have a right child), but the rebalancing is the same. This process is called *rotating* the tree, and is the most work we have to do in insertion. On the bright side, since the topmost node is still black, we can stop here and do no more work!

If we encounter the mirror of any of these situations, we do the same thing in mirror.

Since we never repeat the process except on a parent node, and since red-black trees ensure there are never more than o(log(n)) parents above a leaf node, this operation is o(log(n)) - much better than the o(n) array insertion from before!

Deletion is more effort than this.

----**part5 - Deletions**----

The first step in deleting a node is to find a node we actually don't mind removing - namely, one with at most one child. This first step will give us two special elements, the *deleted* node and the *replacement* node. We have two cases; B is being deleted in both:

    
    C        C   >    
   /        /    >     C 
  B    or  B     >    /    B deleted, A replacement 
 /          \    >   A 
A            A   >    
 In this case, we delete B and replace its location in the tree with A, which may be NULL. This also applies if mirror-reversed, or if B was the root of the tree.

    
      C   >   
     /    >         C 
    B     >        / 
   / \    >      "F"  
  A   D   >      / \ 
     /    >     A   D 
    .     >        /    F deleted, G replacement, F's object now held by B 
   /      >       . 
  E       >      / 
 /        >     E 
F         >    / 
 \        >   G 
  G       > 

If B has two children, we take the rightmost one and follow it left until we cannot go left any more; this node, F, must be the first node after B in the ordering. We swap the items that B and F are holding, so in effect B becomes F, delete the original F, and replace it with its right child, G, which may be NULL.

If the deleted node was red, we do not have a problem - the tree must now be balanced. Similarly, if the deleted node was black but the replacement was red, we can just color the replacement black and be done.

If the deleted and replacement nodes were both black, we must re-balance the tree starting from the replacement node. If the replacement node is NULL, ie the deleted node was childless, the parent of the replacement node referred to in the following is just the parent of the deleted node. We shall start our inspection with the replacement node. (The motivation for the following is that the node we are inspecting is effectively colored *double black*, and we need to move one of those black colors to a higher node, or remove it if we can push it to the root node.)

Firstly, note that the inspected node **must** have a *sibling*, a child of its parent that is not itself. We have five cases to consider, depending on the color of the parent, the sibling, and the sibling's children (considered to be black if they are NULL).

    
  **B**       >     **B** 
 / \      >    / \ 
**I**   **D**     >   **I**   D 
   / \    >      / \ 
  **C**   **E**   >     **C**   **E** 
 If every node involved is black, color the sibling red and start again at the parent node - unless the parent node is the root node, in which case we just stop.

    
  **B**       >       **D** 
 / \      >      / \ 
**I**   D     >     B   **E** 
   / \    >    / \ 
  **C**   **E**   >   **I**   **C** 
 If every node except the sibling is black, rotate the tree as shown, and now the inspected node must be in one of the last three cases.

    
  B       >     **B** 
 / \      >    / \ 
**I**   **D**     >   **I**   D 
   / \    >      / \ 
  **C**   **E**   >     **C**   **E** 
 If every node except the parent is black, we can swap the colors of the parent and sibling and stop our efforts.


    
          >     *B* 
  *B*       >    / \ 
 / \      >   **I**   **C** 
**I**   D**     >        \ 
   / \    >         D 
  C   **E**   >          \ 
          >           **E** 
 If the right child of the sibling is black but the left red, then regardless of the color of the parent we rotate as shown, giving us the last case of all.


    
  *B*       >       *D* 
 / \      >      / \ 
**I**   **D**     >     **B**   **E** 
   / \    >    / \ 
  *C*   E   >   **I**   *C* 
 Finally, if the right child of the sibling is red then we rotate as shown, ensuring the top node remains whatever color it was before, and stop our efforts.

This completes the re-balancing of the tree; it involves no more than 3 rotations in any situation, and since it is only ever repeated on the parent of an inspected node, we can conclude that, like insertion, deletion is an o(log(n)) operation.

----**part6 - Where Red-Black trees are (and are not) used**----

Red-black trees are used in many C++ libraries to implement their storage types (map, set, etc). Their use is almost guaranteed by the ANSI/ISO demands upon execution times of the searching/insertion/deletion algorithms. Because they rely solely upon an ordering, they can use any ordered space for keys, but you cannot mix different key types in the same storage object.

Cocoa's FoundationCollections, on the other hand, do not use red-black trees, but are instead based on DesignHashTable; Apple's Senior Cocoa Software Engineer professed not to know how efficient their implementation of hash tables is.

KTMatrix (see DesignMatrix) may use a Red-black tree in the future instead of hash tables; this is being looked into.

----**appendix - References**----
The following frameworks have implementation of red-black trees: 
* EDCommon ( http://www.mulle-kybernetik.com/software/EDFrameworks/ ) (EDSortedArray class)
* CHDataStructures ( http://cocoaheads.byu.edu/code/CHDataStructures ) ("CHRedBlackTree" class)
 

Also, the following frameworks have implementation of AVL trees, which are self-balancing binary search trees, just like red-black trees: 
* CHDataStructures ( http://cocoaheads.byu.edu/code/CHDataStructures ) (CHAVLTree)
* SortedDictionary ( https://code.google.com/p/cocoa-sorted-dictionary/ )
* OFC ( https://code.google.com/p/ofc/ ) (DAvlTree)
 

I trawled the following in my quest to determine the RB insertion/deletion algorithms: 
* http://www.eli.sdsu.edu/courses/fall96/cs660/notes/redBlack/redBlack.html
* http://www.ddj.com/documents/s=1049/ddj9204c/9204c.htm
 My thanks to their authors for not being too impenetrable.

-- KritTer; comments appreciated

----comments----

A Five-Star page IMHO -- peacha

Thanks, peacha! -- KritTer

Nicely summarized.  You might want to look at AVL (Adelson-Velskii and Landis) trees also.  Their implementation is similar to
red-black trees.  They are not perfectly balanced, but pairs of sub-trees differ in height by at most 1, maintaining an O(log n) search time. Addition and deletion operations also take O(log n) time.

