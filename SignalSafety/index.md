---
layout: page
title: SignalSafety
---



Signals are a common UNIX facility which are used to signal a process about certain events. Common signals that programmers see are SIGSEGV (segmentation fault), SIGBUS (bus error), SIGABRT (abort program), SIGTSTP (stop process, what happens when you press ^Z in Terminal), and many others.

It's possible to install a signal handler on most signals, so that a function gets called when the signal is delivered. This can be used so that the user can make your program reload its configuration files (by trapping SIGHUP), to take action when writing to a pipe that's been closed (SIGPIPE), and for many other useful tasks.

This page is about writing signal handlers, which is a way to execute code when signals are delivered. If you're getting a signal that you don't understand or your application is terminating unexpectedly, see SignalsSentOnCrash.

Signals are delivered asynchronously, much like interrupts, and so there are a great deal of restrictions placed on the code which runs. Many of these restrictions are much like ThreadSafety, in that you have to account for the fact that your signal handler could run at any moment while other code is running, causing weird problems. http://goo.gl/OeSCu

Here is an example of code which is safe:

    
volatile int x = 0;

void handler(int signal) {
    x++;
}

int main(void) {
    signal(SIGHUP, handler);
    while(1) {
        sleep(1);
        printf("x = %d\n", x);
    }
}


Just like with threads, if the signal handler is writing to a primitive and the main function is only reading, everything is safe. Well, as long as no one else sends a SIGHUP: if that were to happen, then the above example has the potential of a race condition. 

Now for a broken example:

    
volatile int x = 0;

void handler(int signal) {
    x++;
}

int main(void) {
    signal(SIGHUP, handler);
    while(1) {
        sleep(1);
        x++;
        printf("x = %d\n", x);
    }
}


This is broken for the same reason that it would be broken with two threads. The operation     x++ is not necessarily atomic, but can be divided into several pieces:

    
read x from memory into a register
increment the register
write x back into memory


If the signal handler is invoked while the main function is in the middle of this update process, an increment will be lost. Worse, the read and write of     x is not guaranteed to be atomic (for that we would need to use     sig_atomic_t).  Let's try to fix this by adding a simple lock. We'll assume that we have a     TestAndSet function which atomically sets a variable to 1 and returns its old value. Then we write our new program like this:

    
volatile sig_atomic_t lock = 0;
volatile int x = 0;

void handler(int signal) {
    while(TestAndSet(&lock)) ;
    x++;
    lock = 0;
}

int main(void) {
    signal(SIGHUP, handler);
    while(1) {
        sleep(1);
        
        while(TestAndSet(&lock)) ;
        x++;
        lock = 0;
        
        printf("x = %d\n", x);
    }
}


If we were working with threads, then everything would work as expected. But with signals, this not only fails to solve the problem, but it actually makes it *worse*. Why?

Threads run more or less simultaneously. On a multi-processor system they might really run simultaneously, but even on a single-processor system, the OS makes sure that every thread gets a chance to run. So while a thread might get stuck in the while loop for a while, eventually the other thread will get a chance to run, and it will unlock the lock.

Signals, however, don't run simultaneously. While the signal handler is running, the main program is completely stopped. If the handler is invoked while the main program has locked the lock, the handler will spin forever waiting for the lock to be unlocked, while at the same time the main program is stuck waiting for the handler to end. Deadlock! If this situation ever happens, your program will completely freeze. So now we see that signal-safe code is even more restricted than thread-safe code.

How do we fix it? For our example program, we can fix it by adding an auxiliary variable, like so:

    
volatile sig_atomic_t lock = 0;
volatile int x = 0;
volatile int y = 0;

void handler(int signal) {
    if(!lock)
        x++;
    else
        y++;
}

int main(void) {
    int temp;
    signal(SIGHUP, handler);
    while(1) {
        sleep(1);
        
        lock = 1
        x++;
        lock = 0;
        
        temp = y;
        y = 0;
        
        lock = 1;
        x += temp;
        lock = 0;
        
        printf("x = %d\n", x);
    }
}


Here, we have a sort of asymmetric lock. Instead of waiting for the lock to be free, the signal handler uses it to decide which variable to increment. The main program then manipulates the lock to ensure that it can always reliably read or modify the variable it's interested in while it performs the sum of the two.

But you say, this counter is nonsense. I just want to print out a notice that my signal was received, nothing more. I'll just write this code:

    
void handler(int signal) {
    printf("got signal %d\n", signal);
}


This code is fine, right? None of this nonsense with locks or counters or anything. No! It's not safe because you're calling     printf(), and who knows what it does inside. In fact     printf() probably does some locking internally on the file stream, so if the signal is delivered while your program is in the middle of another call to     printf(), kaboom, deadlock!

What do we do? In this case, we'll have to do everything manually using functions which we know to be safe. In this case, we take advantage of the fact that     write() is safe:

    
void handler(int signal) {
    char text[] = "got signal 00\n";
    text[11] += (signal / 10) % 10;
    text[12] += signal % 10;
    write(STDOUT_FILENO, text, 14);
}


The key word is "async-signal safe". If you see a function documented as being "thread safe", you know that you can call it simultaneously from multiple threads. If you see a function documented as being "async-signal safe", then you know that you can call it from a signal handler without blowing up your program. A fairly complete list of signal safe functions can be found in     man sigaction.

The trick is that a *lot* of code is not async-signal safe. Since it's so much harder to write, very little code is async-signal safe. For example,     objc_msgSend() uses locks and so is not async-signal safe, meaning that you cannot use *any* Objective-C code in a signal handler. You can't use Objective-C, you can't call     malloc(), you can't touch CoreFoundation or most of libc.

How do you get anything accomplished in a signal handler, then? The best bet is usually to do as little as possible in the handler itself, but somehow signal the rest of your program that something needs to be done. For example, let's say you want to reload your configuration file when sent a SIGHUP. If your program never blocks for long, we could write our program like this:

    
volatile sig_atomic_t gReloadConfigFile = 0;

void handler(int signal) {
    gReloadConfigFile = 1;
}
...
while(!done) {
    DoPeriodicProcessing();
    if(gReloadConfigFile) {
        gReloadConfigFile = 0;
        ReloadConfigFile();
    }
}


What if your program often blocks on input or a socket or something of that nature? All is not lost, however, because delivering a signal will automatically unblock your program if it's in the middle of a blocking     read(),     select(), or similar. You could write your program like this:

    
volatile sig_atomic_t gReloadConfigFile = 0;

void handler(int signal) {
    gReloadConfigFile = 1;
}
...
while(!done) {
    if(gReloadConfigFile) {
        gReloadConfigFile = 0;
        ReloadConfigFile();
    }
    // X
    select(...);
    ProcessData(...);
}


Looks good, right? If your program is blocked in the     select(), the signal will dislodge it and the app will reload its configuration file. If the program is busy processing data when it comes in, then the configuration file will be reloaded before you get back to the     select(). But... you guessed it! This code isn't completely safe!

The key is the     // X comment. If the signal is delivered in that spot, *after* the check but *before* entering the     select(), the     select() will still block, and the configuration file won't be reloaded until some input arrives, which could be much later.

What do we do about this? The best way is to use a signaling mechanism which can be integrated into the     select() call, namely a pipe. You can create a pipe using the     pipe() system call, then read from it in the     select() and write to it in your signal handler. By using the pipe instead of a global variable, you ensure that the     select() never blocks when a condition needs to be taken care of. Implementation of this scheme is left as an exercise to the reader.

If you do use this scheme, you have to be careful. (I can hear you saying, "What now???") Make sure to set your pipe to be non-blocking, otherwise your pipe could fill up in the signal handler before its emptied in your main program, and you'll deadlock. If the write fails because the pipe is full then that's fine anyway, because that means there's already a signal sitting in the pipe ready to be received the next time you go through your app's main loop.

----

What about signals in Cocoa? In fact, a Cocoa application's runloop, managed by CFRunLoop, is much like the basic     select()-using loop in the very last example above. (And if you think that this was just a coincidence, think again!) This means that you can use a similar strategy to notify the main event loop. Make a pipe, and wrap its read end in an NSFileHandle and have it notify you when new data arrives. This way, delivery of a signal will cause your code to get notified as soon as your main event loop is free, and you can use as much Objective-C and Cocoa in your NSFileHandle notification as you please.

----

SignalSafety is also related to writing proper code in the child process after calling     fork(). This relationship is discussed on the ForkSafety page.

