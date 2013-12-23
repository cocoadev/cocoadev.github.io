---
layout: page
title: NSString
---

See also: http://developer.apple.com/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html

Strings in Cocoa article : http://www.macdevcenter.com/lpt/a/978

----

NSString topics:
[Topic]

----

You will come to love NSString - It is a very useful class.  It deals with strings!

The nice thing about using the @"" notation for initializing NSString objects is that if you create two of them and their content is the same, the runtime uses the same storage for them.  (Note, this is not guaranteed and certainly may not be the case across compilation unit or library boundaries.  It is a performance optimization and the fact that it happens sometimes should not lead you to believe that you can safely, for example, use "==" to compare strings if you know they're both from string constants.)

When dealing with strings, sometimes you will need to deal with CharacterEncoding.   

If you want to create an @"..." form that's not plain ASCII, consider using universal character names (UCNs).  For example, @"\u2605" is a string containing one character, which is Unicode's "black star" (hex. code 0x2605).

----

In C, you can break a long string up onto different lines like so:
    
 char *s = "This is line 1\n"
           "This is line 2";


You can do the same thing with NSString constants:
    
 NSString *s = @"This is line 1\n"
                "This is line 2";


----

There is a function in Foundation called _NSMakeFSRefFromString. I haven't used it myself -- yet -- but I'm guessing it takes one parameter, an NSString*, which is a pathname, and returns an FSRef.

I think this is the only easy way to do this conversion, though I know of a CFURL function which goes the other way.

Correction: there is an     FSPathMakeRef function.

----
Does the NSString returned by @"" need to be released, or is it autoreleased?

Neither. @""-strings are of class NSConstantString, and thus act like atoms in lisp; they hang around. That is, if you use @"cow" in two separate places in your code, they will be referencing the very same object.

I don't think -release or -autorelease does anything to either of them.

Over-releasing one might be bad. You should essentially treat constant strings as if they were autoreleased objects, and balance retains/releases appropriately.

Storage-wise, I think it makes sense to think of @"" literals the same way as "" literals: they are in memory the entirety of your program's lifespan.  And that's the way it should be.  If you "released" a string constant, the next time the code that uses that literal gets called, the object wouldn't be there anymore!  You wouldn't "release" an integer constant, or a float constant, for example, if you had some function "int f(int x) { return x+1; }", it wouldn't make any sense to "free" the "1" part of the code!  It's part of your program image!  A string literal really isn't any different.  Of course since NSString is an NSObject you can call retain on it, but I like to think of that as only making the API consistent with other NSStrings which may be allocated dynamically.

----

**- dataUsingEncoding:allowLossyConversion:** only includes the BOM in NSUnicodeStringEncoding, not NSUTF8StringEncoding.

* huh?  And shouldn't this go in CharacterEncoding's, perhaps?*

I will clarify. Unicode has a character called the byte order mark, or BOM. This character appears at the start of the text. It serves two purposes: it signifies that the following is Unicode of one sort or another, and it signals endian-ness.

The description of **- dataUsingEncoding:allowLossyConversion:** says that NSString adds the BOM when creating data from a Unicode encoding. But the term "Unicode encoding" does not apparently mean UTF-8. I was just noting that. -- DustinVoss

"The BOM is completely optional for UTF-8 - endianness has no meaning with UTF-8 since it's basically a stream of single bytes."

**Yes, it's optional, but it can be important to know if it will be there.  As Dustin said, the BOM also serves to flag text as UTF-8.  Without the flag, you have to know that information externally.**

I'd like to advise you to this document: http://www.ifi.unizh.ch/mml/mduerst/papers/PDF/IUC11-UTF-8.pdf and in there to the topic "Heuristic Identification" Where it says that you can detect utf-8 heuristically with *very* high accuracy of about 0.000081 for a string being wrongly detected as unicode if it is 15 (!) characters long. (And of course the accuracy increases for longer strings). So the BOM indeed should not be there, as it defeats quite a few uses of textfiles that could greatly benefit from utf-8 (think shell-scripts), etc. -- MartinHaecker

----
Is there a way to avoid stringByAppendingString: ?
It's annoying if you need to append more than one string to another.

----

You can use:     [NSString stringWithFormat:@"%@%@%@", str1, str2, str3]. If you need to append lots of different data to a string from various sources, you may look at CocoaSTL, it allows to copy into a string, e.g.:
    
 NSMutableString* str = [NSMutableString string]; // sink!
 
 // add 20 spaces
 std::fill_n(back_inserter(str), 20, ' ');
 
 // append the contents of another string
 std::copy(beginof(other), endof(other), back_inserter(str));
 
 ...


Or if you're not using C++, you can use NSMutableString and accumulate strings into there.

----

In conformance with the NSComparisonMethods protocol, NSString implements these methods:     - (BOOL)isLike:(NSString *)object;
- (BOOL)isCaseInsensitiveLike:(NSString *)aString;
These methods compare the receiver to *aString*, which can contain the * and ? wild-card characters.  * matches 0 or more arbitrary characters, ? matches one arbitrary character (i.e. bash-like, not perl-like).  Brackets denote a set of characters.  For example,     [@"b" isLike:@"[a-c]"] has the precise meaning of     [@"b" isLike:@"[abc]"] and both evaluate to     YES.  As far as I can tell, the other bits of bash "globbing" (e.g. ^ to reverse sense of match, curly braces) are not supported.

The existence of the methods is documented, as is that they match "a pattern".  What a pattern consists of is not documented. 

*Okay, technically, the methods are documented, but the fact that NSString implements these methods in not easily ascertained, assuming you even knew about NSComparisonMethods.*

----



Now, if you want to get *really* undocumented, check out these private NSString methods.

    
 - (BOOL)matchesPattern:(id)aPattern;
 - (BOOL)matchesPattern:(id)aPattern caseInsensitive:(BOOL)flag;


Appear to be full-on, built-in regular expressions.  As with all undocumented methods, you cannot count on their continued existence or even if they actually work (in all cases).  They are not apple supported.  They are, in fact, apple discouraged.

----

Objective-C desperately needs some syntactic sugar for strings. Compare Java:

    
 String str = "This is";
 str += " very simple ";
 String strlong = str + 2 + " use";
 // This is very simple 2 use


In Objective-C:

    
 NSString * str = @"This is";
 str = [str stringByAppendingString:@" a little more complex "];
 NSString strlong = [ str stringByAppendingString: [NSString stringWithFormat:@" and verbose %d", 2] ];
 // This is a little more complex and verbose 2

-- SimonWoodside

*You can always use Objective-C++ and the C++ string object, works pretty similarly.*

    
 NSString *str = [NSString stringWithFormat:@"This %@ is much less %s.", @"example", "contrived"];


Format strings are your friend.

Objective-C cannot use that sort of "syntactic sugar" because the + operator is already used for pointer arithmetic.  Consider the following Java code:
    
 String str = "Testing";
 String str2 = str + 123;

In this example, str2 is "Testing123".  Now consider objc:
    
 NSString *str = @"Testing";
 NSString *str2 = str + 123;

Note that this compiles just fine.  However in this example, str2 is the object 123*sizeof(NSString) bytes after "str".  (If we try to do anything with it, we'll probably crash).  That's not to say that pointer arithmetic isn't useful.  It just means that the + operator is taken.

*On that note, is there any particular reason why operators can't be messages? IE [str +=:str2];*
---- Well, consider [str ++]; Remembering that str is a pointer, is this a message to str or is it a pointer increment? It's ambiguous.

----
Given the brackets around the statement, it's a message pass.  If it were a pointer increment it'd be str++; not [str ++];  It makes sense to me that whenever you see brackets it'd be treated as an objective C message and when not, then by golly, let the underlying C do its thing.

>>> what would it do with this code then: a = x[str++]; where x is an array variable and str is an array index? It's more complicated than "whenever you see brackets" since square brackets are also already used for other things.

----
This is a very dirty hack and it's not the prettiest syntax, but a while back I was experimenting with overloading operators using Objective-C++ to implement such syntactic sugar. What I found is that you can't overload, for instance, the binary + operator for just pointers. However, you can overload it for two structs, i.e., NSString instead of NSString*. If you pass them in as references then this works, but you have to use the pointer dereference operator. So, like I said, it's still not quite pretty.

    
 #import <Cocoa/Cocoa.h>
 #import <iostream>
 
 using namespace std;
 
 NSString* operator + (NSString& left, NSString& right) {
 	return [&left stringByAppendingString:&right];
 }
 
 ostream& operator << (ostream& out, NSString* string) {
 	return out << [string UTF8String];
 }
 
 int main(int argc, char** argv) {
 	NSAutoreleasePool* pool = NSAutoreleasePool alloc] init];
 	
 	NSString* firstString = @"This is an NSString";
 	NSString* secondString = @" appended to another NSString.";
 
 	NSString* combinedString = *firstString + *secondString;
 
 	cout << combinedString << endl;
 }


This little program that I hacked together to test my idea seems to work. I'm not sure if my ostream << NSString* operator should be disposing of the C-string afterwards, but I only wrote it for the output test. Make of it what you will, but + works.:) -Eris

----
I'm not very familiar with GNU C++, but do you know *why* you can't overload an operator for two pointers? Is it a limitation of GNU? Won't this code crash if firstString or secondString is nil?

----
It's a limitation of the language. My C++ is fuzzy but I believe it has to do with type inference not working well when both are pointers. With appropriate casting, an     NSString * can be converted to a     void * or a     std::vector<float> * or any other sort of pointer, which is not generally the case with other types. In effect, an overloaded operator needs at least one user-defined type, and pointers are never counted in this even if the pointed-to type is user defined.

As for crashing, it won't crash because the strings never really get dereferenced in the machine code, the     * is just trickery to convince the compiler about the types:     left and     right are in reality pointers in the overloaded operator, so nothing will blow up for     nil. However, I don't know if this behaviour is guaranteed by the language so it's possible that a mean compiler might crash on this code. (It's possible that     g++ will crash on it, since I haven't really tried it, but I'm pretty sure it'll work.)

----

It does seem like a bit of assumption that nil pointers won't get dereferenced in this code--that's probably an implementation detail, as you said. Does this code work at all with (id)? I'm aware that's still a pointer type, but one can hope there's some magic to it in the [[ObjC preprocessor. Perhaps if so, you could write something generic like this:

    
 /* header file */
 typedef const char *OperatorID;
 
 @interface NSObject (CPlusPlusOperations)
 - (id)performOperation: (OperatorID)op withOperand: (id)anObject;
 - (void)cannotPerformOperation: (OperatorID)op withOperand: (id)anObject;
 @end
 
 /* ---------- */
 
 /* implementation file for glue code */
 
 @implementation NSObject (CPlusPlusOperations)
 - (id)performOperation: (OperatorID)op withOperand: (id)anObject {
 	[self cannotPerformOperation: op withOperand: anObject];
 	return nil;
 }
 
 - (void)cannotPerformOperation: (OperatorID)op withOperand: (id)anObject {
 	[NSException raise: @"UnsupportedOperatorException" format: @"%@ cannot perform operation %s with object %@", self, op, anObject];
 }
 @end
 
 id operator + (id left, id right) {
 	return [left performOperation: "+" withOperand: right];
 }
 
 id operator - (id left, id right) {
 	return [left performOperation: "-" withOperand: right];
 }
 
 id operator == (id left, id right) {
 	return [left performOperation: "==" withOperand: right];
 }
 
 /* ---------- */
 
 /* implementation file for NSString */
 
 @implementation NSString (CPlusPlusOperations)
 - (id)performOperation: (OperatorID)op withOperand: (id)anObject {
 	if (0 == strcmp(op, "+")) {
 		anObject = [anObject description];
 		if (anObject)
 			return [self stringByAppendingString: anObject];
 		else
 			return self copy] autorelease];
 	} else if (0 == strcmp(op, "==")) {
 		anObject = [anObject description];
 		return (anObject && [self isEqualToString: anObject]);
 	} else {
 		[self cannotPerformOperation: op withOperand: anObject];
 		return nil;
 	}
 }
 @end


Then, classes could implement whatever operators they wanted just by implementing the informal     [[CPlusPlusOperations protocol. --JonathanGrynspan

----

That code I posted was just a Quick And Dirty Proof of Concept sort of thing. I don't actually know how well it works outside of the brief test code I wrote! :) Unfortunately, id _is_ just a pointer type, so you'd still need to dereference it. Oh well.

I'm going to give your code a try though and see what I can make it do! I'll try a little more hacking around with the pointers.

Another thing people here might be interested in with Objective-C++ is defining     ostream& operator << (ostream&, id). I've found it useful for outputting text in console apps because I'm not aware of any Cocoa text output functions other than NSLog (with that awful timestamp). I might post code for it later when I can be bothered, but for now I'll leave it as an exercise for the reader. :) -Eris

----

What about defining a conversion operator that wraps an id in a simple structure (which it then returns on the stack)? Then the other operator functions could be defined to work on an id and an id wrapper. Would that work? -JonathanGrynspan

----

How would you define the conversion operator? You can't use     id::operator idWrapper () {} because     id is not a C++ type. So you can create a conversion operator to turn idWrapper into id, but not the other way around. --RobRix

----

id is just a typedef for a C structure. It's not a magic Objective-C-specific type. Can't one define a conversion operator for a structure type? -JonathanGrynspan

----
Yes, but id is not a typedef for a C structure, it's a typedef for a *pointer to* a C structure, which changes things completely.

----

Touche. -JonathanGrynspan

----

Caution: componentsSeparatedByString: on the iPhone first unescapes all the url-encoded string, and then splits.. 

----

Caution: [NSString hash] uses a very bad hashing algorithm, and on 10.4 is almost unusable - giving the same hash for strings longer than 27 characters that have changes in the middle...

