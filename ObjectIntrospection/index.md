---
layout: page
title: ObjectIntrospection
---



Introspection is the ability of AnObject to reveal and alter information about itself as an object. For example, in ObjC, its class and superclass, various messages to which it responds, FormalProtocols to which it conforms, and its data.

----

Anybody know a Cocoa way to learn what FormalProtocols a class conforms to?

----

    +[NSObject conformsToProtocol:]

If you want to know *all* the protocols an class conforms to, then I've never used it, but a protocol list is one of the instance variables of a class object.       *<class object>*->protocols  should get you a variable of type     objc_protocol_list *.

----

Note that this list will only tell you the protocols that the class in question declares it conforms to. To get all the protocols it conforms to, you'll have to iterate over the superclasses' lists as well.

----

OK, thanks, but does anyone understand the type definition of objc_protocol_list?
    
/** Excerpt from <objc/objc-class.h> **/
struct objc_protocol_list {
	struct objc_protocol_list *next;
	int count;
	Protocol *list[1];
};

What's up with the one-element array? Is that the real list (of size     count), or is it just one of the protocols, forcing me to iterate through the     next pointer? Or are they both there? Has anyone used this stupid syntax before?

----

It's the real list of size     count. The one-element array hack is to allow open-ended structs. Newer versions of GCC have more explicit support for this, I believe, but traditionally it's done with a one-element array at the end of the struct. Having an array there allows you to access the list as an array, and the struct is allocated as     ( sizeof(objc_protocol_list) + sizeof(Protocol*) * (numberOfProtocols - 1) ). I can't figure out that the     next pointer actually does anything. It may be there for either backward or forward compatibility.

----

The list is probably a mixture of arrays and linked lists, thus the next pointer. To enumerate all protocols in the list, you'd do something like this:
    
void DoSomethingWithProtocolsInList(struct objc_protocol_list *l) {
   if(!l) return;
   for(int i = 0; i < l->count; i++)
      DoSomethingWithProtocol(l->list[i]);
   DoSomethingWithProtocolsInList(l->next);
}


----

I had assumed something along those lines, but I can't find any case in which the next list is set to anything meaningful. It doesn't point to the superclass's protocols, and any given class's protocols always seem to be completely covered by     list (at least I can't find any case to disprove this hypothesis).

----

I'd bet that the linked list would have multiple nodes if the class has a category which declares additional protocols. Have you checked that?

----

You are correct, "next" will be non-null when a category adopts extra protocols into a class.

Here's another problem:  If protocol1 adopts protocol2 and MyClass adopts protocol1, then     [MyClass conformsToProtocol:@protocol(protocol2)] is true, but protocol2 will not be in the list of protocols that we've found so far.  And lo, we see that a Protocol object also has an instance variable     struct objc_protocol_list *protocol_list.  So we need to dig in that direction as well.  I'm not sure how to, since my quick attempt in gdb,     *protocol_p*->protocol_list just gets     There is no member named protocol_list.  Maybe something using @defs?

By the way, I don't think that the runtime is guaranteed by apple to be stable at the depth we're looking..  Even so, good thread. :-)  Should be refactored to FormalProtocols when we're done.

I am a little curious to hear why the OP is interested in obtaining this list.

----

It might be failing because Protocol declares all of its ivars as @private. You could get around this by using @defs and casting, or by simply adding a category to Protocol that returns the protocol_list. If that exists, you could amend my code above like so:
    
void DoSomethingWithProtocolsInList(struct objc_protocol_list *l) {
   if(!l) return;
   for(int i = 0; i < l->count; i++)
   {
      Protocol *protocol = l->list[i];
      DoSomethingWithProtocolsInList([protocol protocol_list]);
      DoSomethingWithProtocol(protocol);
   }
   DoSomethingWithProtocolsInList(l->next);
}

I believe using @defs to get protocol_list would look something like this:
    
((struct { @defs(Protocol) } *)protocol)->protocol_list

But I didn't test it. Anybody else care to? ;-)

I think you're right that this stuff is subject to change, but I have a feeling that this is more of a theoretical worry. There probably won't be much reason to change these details, as they work and are proven.

