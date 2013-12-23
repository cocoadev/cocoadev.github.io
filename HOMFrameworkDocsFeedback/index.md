---
layout: page
title: HOMFrameworkDocsFeedback
---

Hi all,

I'd like to respectfully request feedback for a comprehensive higher order message framework for Cocoa, which is nearing completion (ie, passing most unit tests without memory leaks).

The documentation is available at:

http://www.beaversoft.co.uk/~mikeamy/Documentation.tar.gz

In particular I'd like to know if you think you would use any of the features, any improvements or suggestions, any test cases you can think of that might fail.

Cheers for your time if you read it, anyone who gives useful feedback will be credited in the release.

-- MikeAmy

Oh and Happy Christmas & New Year to you all.

----

Hmm, seems no-one noticed, perhaps my approach was all wrong... oh well, if at first you don't succeed... 

The following is an updated extract from the docs, it tries to sum things up.

All higher order messages are defined for root classes. Most except -all and -forever are capable of returning results.

In the following, "message" refers to some arbitrary message with an arbitrary number of arguments. Where you see "id result =" that means that you don't have to wait for the result (say if it hasn't been computed yet).

 Forwarding
         object forward] message]
 "perform [(encapsulated message) invokeWithTarget:object] (Nothing really special)"
         [[object reforward] message]
 "perform [object forwardInvocation:(encapsulated message)] (Useful for hiding methods behind a proxy)"

 Using Collections

 Referring to elements
         [collection elements]
 "each element in collection"

Number elements
         [[[NSNumber ints:1 to:100]
 "NSNumbers from 1 to 100 representing ints, all other primitive types (chars, shorts, longs, floats, etc) are also available."

 Collection operations
 Note that this is a subset of the possible collection manipulation higher order messages. You can also use collections as iterating arguments. Refer to the documentation for full details.
         [collection elements] all] message]
 "send message to each element in collection"
         [[[collection elements] collectEach] messageReturningObject] 
 "collect each result from sending message to each element in collection in a matching collection"
         [[[collection elements] selectIf] messageReturningBOOL]
 "select elements from collection if [element messageReturningBOOL] returns YES, in a matching collection"
         [[[collection elements] rejectIf] messageReturningBOOL]
 "opposite of selectIf"
         [[[collection elements] detectIf] messageReturningBOOL]
 "return first element that meets criteria"
         [[[collection elements] detectIfNot] messageReturningBOOL]
 "return first element that doesn't meet criteria"
         [[[collection elements] combine] combineMessage:initialElement]
 "combine all elements in collection into one object using combineMessage:, whose target and first argument types are the compatible"

 [[MultiProcessing
         id result = object inParallel] message]
 "perform [object message] using symmetric multi processing if multiple processors are available, otherwise just perform [object message] normally. Result is available immediately."
         id result = [[object inNewThread] message]
 "perform [object message] in a new thread."

 Laziness
         id result = [[object lazily] message]
 "return result but don't bother to perform message until the result actually gets used"
         id result = [[object eagerly] message]
 "same as lazily, but compute the result anyway if there is no other processing to perform"

 Scheduling
         id result = [[object scheduleFor:date] message]
 "schedule message delivery for date" 

 Getting invocations
         [[object getMessage] message]
 "return [object message] as an [[NSInvocation instead of performing it"

 Wrapping primitives
         object wrapResult] messageReturningPrimitive]
 "Wrap primitive return values as objects"
         [[object unwrapArguments] messageWithPrimitive:object]
 "Unwrap object arguments into primitives where necessary"

 Thread safety
         [object threadSafe]
 "Create a thread-safe access point to object"

 General control
         [[object repeat:n] message] 
 "perform [object message] n times. Result is result of last message"
         [[object while:condition] message]
 "perform [object message] while condition evaluates to YES"
         [[object until:condition] message]
 "perform [object message] until condition evaluates to YES"
         [[object forever] message]
 "perform [object message] forever"

 Debugging, testing & performance
         [object logMessages]
 "log every message received by object to standard output"
         [[object mustThrow:exception] message]
 "Ensure that object throws exception when sent message"

 Handling exceptions
         [[object ignoreExceptions] message]
 "try performing [object message] but ignore any exceptions"
         [[object logExceptions] message]
 "try performing [object message] but log any exceptions"
         [[object ignore:exceptionName] message]
 "perform [object message] ignoring specified exception"
         [[object ignoreFromSet:exceptionNameSet] message]
 "perform [object message] ignoring any of a specified set of exceptions"

 Autorelease pools
         [[object inNewAutoreleasePool] message]
 "perform [object message] using a new autorelease pool"

 Sorting
         [[collection sort:direction] message]
 "sort elements of collection using message"

 Repeating at specified interval
         id result = [[object repeatWithInterval:interval] message]
 "perform [object message] every interval seconds"

 State pattern
         [object changeClassTo:[[[NewState class]] 
 "safely change class of object to NewState"

 Observation
         [invocation invokeWhenever:object posts:notification]
 "invoke invocation whenever object posts notification"

 Target action extension
         [invocation attachTo:object]
 "attach invocation to object using the target action mechanism"

----

