---
layout: page
title: CocoaNetworkClientProgrammingAndDesign
---



I am writing an IRC client in order to really leran Cocoa.
Now, I am doing this in Pure Cocoa, that is, Only IB Nibs, no Carbon, and, if I can pull it off, no BSD or CoreFoundation (I know that CF IS Cocoa, but...)

I think I have made up my mind to use OmniNetworking as my networking code. I considered writing light weigth socket classes, but I would have to spend time debugging them, something I do not look forward to. Besides I am planning to use threads, and I know that Omni is thread-safe (More on this later).

Now, during the last several days I have been designing my app and a lot of questions started popping up.

I have a class that represents an active connection to the server, let's say IRCConnection. Now I was planning to write a method in this class, say -runEventLoop, which would be detached as a separate thread each time a new connection is made by the user.
Now, in -runEventLoop, I planned to put code to loop trying to read the socket. When a complete message is received (a string followed by a CR LF) I would post a notification. But there's a problem with this: at any given time, the user could choose to send something to the server... Now: how can I coordinate the reading/writing of the socket? Should I use non-blocking reading (within a semaphore) and set up a buffer to hold all the data to be sent, which would be sent after the reading call returned ?

Sorry if this is very complicated. If you don't understand my english please post any question you have and I will try to make it clearer.

Another totally different approach would be not to use threads at all, with something like select (); But I don't know if that would work very well.

Anyway, I was looking for any kind of feedback anybody could give me. I am not very experienced with threads or async socket programming (apart from a little server I wrote with BSD Sockets).
Any kind of question, suggestion, comment, etc .. would be extremely appreciated.

-- NicolasOjedaBar

----

There is a pre-written lightweight BSD sockets library you might be interested in (SmallSockets).  Although, there's certainly nothing wrong with OmniNetworking.

One possible option for your read/write dilemma, is you could have one socket for the IRC connection, and another that is used for internal application communication.  When the user types something, you can post it on the "internal" socket.  Then, in your thread, you can do a blocking select() on the IRC socket and the internal app socket, so that activity on either will wake the thread.

I've never used this technique, but it seems like it would be fairly efficient, and that it would do what you want...

You might also want to check out what we have on ThreadSafety.

-- StevenFrank

----

Hi. Thanks for the suggestions. After meditating on the issue I have decided on the following design: My connection object has a method which is detached as a new thread. In this method I check weather or not I have data to be read from the internet socket. Then, after I do the necessary readings, I check a queue which has data to be read (input by the user) and send everything. The Connection object has methods to enqueue data to be sent.

I think this makes a clear design and efficient run time. Anyway, I am still considering different design choices.

-- NicolasOjedaBar

----

You'll probably have to have a seperate thread for your Connection object, no matter what; I don't see any way around that.  However, you could use select() with multiple sockets in your Connection object.  This would allow you to connect to multiple IRC servers at the same time without having to spawn a new thread and Connection object for each one.

(Btw, does anyone know how to call select() from Cocoa?  I didn't see anything on the ADC web site..but I'm probably not looking in the right place.)


-- CliffCrawford

----

Well, ObjectiveCee is still just C at heart, so you just call it the same way you would from C.  :)  Take a look at the SmallSockets source to see how you can use BSD sockets from Cocoa.

-- StevenFrank

