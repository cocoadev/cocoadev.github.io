---
layout: page
title: GCCBlocks
---

I've been wanting Blocks in ObjC for a long time. These would be syntax-supported closures, like the ones in PortableObjectCompiler, that are discussed on the CodeBlock page. I believe my approach is different from the other ones proposed here, and I want to see whether it makes any sense. (And I want to see if I can trick some unsuspecting people into implementing it for me.)

The proposal is to integrate Block support directly into the compiler by modifying GCC. This would involve some syntax support and make the compiler depend on some externally-provided classes, much like the current @"" constant-string syntax, except more complicated.

The syntax would resemble the constant string syntax. Writing @{...} would create a new Block object. The code inside the Block would be able to reference local variables inside the creating function; changes from one would be reflected in the other, and the ability to reference would persist even after the enclosing function had returned.

Normally, this is done by moving all local variables that are referenced in a Block off the stack and into a heap-allocated object, then running a garbage collector to clean up these "local variable" structures. We don't want to run a garbage collector just for this. However, I believe we can co-opt the Cocoa refcounting system into working in this situation as well. The @{} syntax would return an autoreleased Block object, and then the normal refcounting rules (retain to keep it around later, etc.) would apply.

Two support classes would be needed, Block and BlockVariables. (They should probably be prefixed with something like NS.) BlockVariables is what contains the local variables that have been moved onto the stack. It's a class so that we can use Cocoa's built-in thread-safe refcounting instead of having to build our own. A Block instance then contains a reference to the corresponding BlockVariables instance and a pointer to the function that implements the contents of the Block.

Enough jabber, let's look at some made-up code:
    
- (void)someMethod:(NSString *)string {
    NSString *lowercase = [string lowercaseString];
    [array do:@{ :obj | NSLog(@"%@: %@", lowercase, obj); }];
    [button setBlockAction:@{ NSLog(@"%@", lowercase); }];
    NSLog(@"%@", lowercase);
}

The compiler, upon running into this, would create some structures and functions to handle everything. Imagine for a moment that the compiler preprocesses the code into something that would compile with today's GCC:
    
struct __someMethodBlockVariables {
    NSString *lowercase;
};

static void
__someMethodBlockFunction1(struct __someMethodBlockVariables *__localVars, id obj) {
    NSLog(@"%@: %@", __localVars->lowercase, obj);
}

static void
__someMethodBlockFunction2(struct __someMethodBlockVariables *__localVars) {
    NSLog(@"%@", __localVars->lowercase);
}

- (void)someMethod:(NSString *)string {
    BlockVariables *__localVarsObj = [[BlockVariables alloc]
        initWithSize:sizeof(struct __someMethodBlockVariables)];
    struct __someMethodBlockVariables *__localVars = [__localVarsObj localVars];
    
    __localVars->lowercase = [string lowercaseString];
    [array do:[Block blockWithLocalVars:__localVarsObj
            function:__someMethodBlockFunction1]];
    [button setBlockAction:[Block blockWithLocalVars:__localVarsObj
            function:__someMethodBlockFunction2]];
    NSLog(@"%@", __localVars->lowercase);
    
    [__localVarsObj release];
}

The local variables that are accessed from Blocks get stuffed into a struct. A BlockVariables object manages this struct but doesn't know anything about the contents. The enclosing method and the functions which implement the Blocks both go through that struct to access the "local" variables. (Local variables that aren't referenced by a Block would be unmodified.)

The BlockVariables object is managed according to the standard refcount scheme. All Block objects retain it, and the local function also must "retain" it until it finishes. Things like return statements would complicate things slightly, but not enormously.

One unsolved problem is how to handle parameters to Blocks. You can see the syntax I used above. Should Blocks only be able to take object parameters? How many? How do you then call a Block with parameters?

I haven't actually done any work on this idea, beyond writing this page. I'd guess that hacking GCC to accept this syntax and generate the proper code wouldn't be tremendously difficult, but I've never hacked GCC before.

Any comments? I'd like to know whether any of this makes sense or if I'm just ranting uselessly. -- MikeAsh

----
There's one major problem with this design, and with blocks in general - what to do with arguments located on stack which are passed by reference? Take this code for example:
    
void function1(void) {
    int i = 3;
    function2(&i);
}

void function2(int *i) {
    [someString async: @{ :obj | [obj appendFormat:@"%d", *i];}];
}


The above code tries to send an appendFormat: message to someString in an asynchronized way (ignore the strange syntax and how its done). After the block gets created function2 and function1 return, which destroys the stack frame of function1. Obviously, this also destroys the int that got passed by reference to function2 and to its block. Now, when someString tries to execute the block and dereference i, it'll get garbage at best.

I don't think there's a good way to work around this problem, and even if this behavior is documented not to work, I bet debugging it will be a nightmare. --OfriWolfus
----

Fundamentally this is no different from passing a pointer into a pthread_create call, or a delayed invocation, or just stuffing it into an ivar. In all of these situations you have to consider pointer lifetime and what you're allowed to do. When somebody passes you something by reference there are already a lot of things you can't do, and using them in a block would be another one. It's another thing the programmer will have to keep in mind but it re-uses the familiar discipline. -- MikeAsh

----

At MyBlocks I implemented something that is quite like this. It doesn't modify GCC to do it though. Feedback would be welcome. :-) -- MartinHaecker

----

Honestly, I'd be happy enough just with writing functions inline. Even if there were nothing lexically special about such a function's scope-- i.e. it wouldn't inherit the scope it was created or called within-- it would be very useful to me. Maybe we should start with that and then work our way up? -- RobRix

----
GCC goes one better and already implements nested functions which can refer to the outer function's variables, so long as the outer function is still on the stack when the inner function is called. This are not full blocks since they go invalid when the outer function returns but they could still be used for all sorts of useful things.

The trouble is that the implementation is recklessly boneheaded and requires an executable stack, which simply does not work on OS X anymore. Simply fixing GCC's implementation of nested functions would make me entirely happy.

I have filed a bug to this end with Apple as rdar://4450943 and it has since been closed, which gives me a continual ray of hope even though I'm sure this is probably just their way of saying "not gonna happen, bud". -- MikeAsh

----
So, even though GCC has "-fnested-functions", this doesn't really mean anything?  I'm assuming that this has something to do with NX protection, and that GCC-4.0 still implements nested functions in the same manner.

Since C doesn't have closures, I'm thinking that implementing them in Objective-C, while difficult, is a better option than breaking the C99 compatibility headaches this will introduce in GCC.

----

My hopes still lie with a preprocessor, honestly. -- RobRix

----
    -fnested-functions just re-enables the capability but it doesn't fix the implementation. It still uses a trampoline on the stack. If you try writing a test program using that flag it will work on PPC but fail on x86 because x86 has a non-executable stack. -- MikeAsh

----
Thinking about it, blocks are just a private case of coroutines. If you require the function containing the block not to return for the block to be valid, you can get blocks without too much troubles using setjmp/longjmp. I bet it'll break the current exceptions implementation though. Honestly, this is kinda useless IMO.

If you want the block to live outside its function's scope you need to somehow preserve the function's stack frame, like Mike explained above. I believe it can be done without compiler support by copying the memory from __builtin_frame_address() to SP, save all registers (which setjmp already does) and store the location of the block's start. Then invoking the block will require copying the stack frame to the end of the stack, restore all registers and set SP to the new location. Not the easiest thing to do, but not impossible. -- OfriWolfus

----
The interesting thing about blocks, though, is the syntax, where you get to write the code inline and (hopefully) refer to the scope of the enclosing function. This obviously requires compiler support. Without that, there's no real advantage over simply writing a regular C function or ObjC method, passing it a context structure, and using the standard callback idiom. -- MikeAsh

