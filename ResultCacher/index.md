---
layout: page
title: ResultCacher
---



This idea comes from function programming and is really a design pattern I suppose. I'm not aware of whether this has been done before in Obj-C or in any other language other then Haskell. I haven't done and extensive search. If anyone knows otherwise then please correct me.

Just found this http://www.apl.jhu.edu/~paulmac/c++-memoization.html

!!!!Warning :- most people probably wouldn't use this because it will not work well with methods which rely on side effects but anyway and IO

A RecultCacher is a proxy which which is passed an object and a list of selectors which should have their results cached. The constructor could read something like:
    
- (ID) initWithObject:(id)pRealObject cacheSelectors:(NSArray*)pSelectos;   // The maximum number of cached results is the maximum value of the type unsigned
- (ID) initWithObject:(id)pRealObject cacheSelectors:(NSArray*)pSelectos cacheLimit:(unsigned)pMaxResult; // sets the maximum number of cached results per selector.


When the proxy receives a message it hash's the arguments and the selector and looks to see if it has the result already. If the result exists already it returns the result (copies the result before returning it if it can). If it doesn't then it forwards the message to the original object. When the object returns it keeps a copy of the result in it's cache ready for the next thing it's called.

**
Some points
**
The number of requests for an argument hash could be recorded. If the limit to the cache is reached only the least requested is removed. The alternative would be to remove the oldest or the one which was accessed the last.

No message which as a void return type would be allowed as they would imply causing a side affect.

**
Not as a proxy
**

This could be implemented as a class on it's own and used for caching within messages. So it would simply perform the task of hashing the arguments and discovering them in a hash table. It could also do the coping of the values too. An interface something like:
    
- (BOOL) resultFor:(NSInvocation*)pCall;  \\ returns YES if there was a cached result. It sets the return of pCall if there was. NO otherwise.
- (BOOL) resultInt:(int*)tResult withArguments:(const char*)frmt, ...;   // need a better solution I think.
- (BOOL) resultFloat:(float*)tResult withArguments:(const char*)frmt, ...;
... \\ all the other variants


The function would have to request it's cache from a Cache singleton. this singleton would be passed the methods selector ideally.
**
Time for an example
**
Ok I don't think this example would work but I hope it illustrates the idea.
    
...
static ResultCacher* cachedFib = [ResultCacher initWithObject:[fib class]];   // Lets assume the we want all the messages to have there values cached.
...

@implementation fib
+ (unsigned int) fib:(unsigned int)n{
    if (n < 1)
       return 1;
    return [cachedFib fib:n-1]+[cachedFib fib:n-2];   // Hopefully we have already called fib on some other number less than n.
}
@end


**
Non-proxy example
**

    

@implementation fib
+ (unsigned int) fib:(unsigned int)n{
    ResultCache* tResults = [ResultCaches cacheFor:@selecctor(fib:) ofClass:[self class]];
    int tResult;
    if ([tResults resultFor:&tResult withArguments:@encode(int), n])
       return tResult;
//else
    if (n < 1)
       return 1;
    tResult = [self fib:n-1]+[self fib:n-2];   // Hopefully we have already called fib on some other number less than n.
    [tResults addResultInt: tResult withArguments:@encode(int), n];
    return tResult;
}
@end


I would appreciate constructive criticism on this.

Stefan Pantos

