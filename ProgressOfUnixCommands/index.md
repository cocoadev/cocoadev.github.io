---
layout: page
title: ProgressOfUnixCommands
---



I was playing with both AppleScript and Python (PythonLang), wraping unix commands like pax gzip and bzip2. It quite easy to build a one click solution to using these tools.

The biggest probelem is creating a progress view for the process. Users like to see what is going on and know how much time is left for a process. But when you run pax -w | bzip2 > outfile on a 100MB file, it could take few minutes, and these programs do not report anything.

I was thinking about a simple sulution, creating a MeterPipe. instead of piping the data trough the unix built in pipe, using my custom pipe, that will update a progress bar while data moving through it.

What do you think about this idea?

--NirSoffer

----

OVERHEAD, OVERHEAD, OVERHEAD!!!!!
Why not just guesstimate what the output file size should be and look at the NSFileSize attribute every five seconds or so. If it's less than five seconds then the question is do you really need a meter? And if it's longer than ten seconds any guesstimation should be enough of a status fix for a user. Of course have the status bar increment smoothly to your guesstimation and then adjust the rate of progress dynamically as you poll the file size every five seconds. This method will not tax your system resources which is always a good thing. 

Oh yeah, I think gzip can tell you what the uncompressed file size is.

----

Uncompressing is easy, but lets take the compression case: You put inside 100MB, and you get somthing between 99.9MB (Folder full of JPEGS) to 1MB output (Tiff files with alot of white backround). One can't guesstimate this kind of job. I can check the output file size for few seconds, and then make get an estimate for the MB per seconds of output file, but I dont know how many MB like that I have, unless I know how many megabytes got in the process.

The question is how much overhead I create when piping data through my application instead of the system pipe. My pipe can do somthing like this (using pseudo Cocoa code):

    
while (newData = [infile readSomeBytes])
// what should be a good lenth for SomeBytes? 8K? 64K? 512K?
{
    // infile and out file are file handles attached to real files or NSPipes
    // dataRead is an instance variable
    dataRead =+ [newData lengh];
    [outfile writeData: newData];
}


at the same time I can run a NSTimer that update the progress bar every few seconds using the value of dataRead

How much overhead that could be compared to something like command1 | command2? The unix pipe has to do similar work anyway.
  
----

Wha? ... how does pollling the filesystem every few seconds not tax system resources?!? This is a terrible idea. CPU time is a system resource, you know, and disk reads are more expensive than memory copies.

In the particular instance of communicating w/ arbitrary CLI tools, the pipe idea is a very good approach. Measure the data as its being fed into the pipe and then you won't have to second guess compressed file sizes. i believe in most unixy-implementations, including ours, pipe reads are limited to 8k. (or was it 4k? I forget exactly but it's really small) If your high level abstraction can loop until its acquired a certain amount of data, say 128k maybe, you might avoid creating a bunch of small ObjC objects. Measure the performance at various sizes and see what works best for you. You might also have to create an autorelease pool to reduce memory thrash.

You shouldn't need an NSTimer for this. Just check the current time in your read/write handler. If enough time has passed, say 1 second (not 5! sheesh!) then update your progress bar.

The additional overhead is one additional pass through the kernel's file handling code, I believe. You should find this about as expensive as command1 | cat | command2. Or if you're really concenred about performance, just write normal pipe(), read(), write() calls using a C function and avoid the ObjC stuff altogether. Just make sure you can message your progress bar once or so a second:

    
//begin pseudo code
long lastSecond = 0; 
off_t totalRead = 0; // this is an 8 byte value

while (bytesRead = read(inPipeFD, &buf, bufsize) {
    struct timeval tv;

    write(outPipeFD, &buf, bufsize);

    // check the date and message. I have some fancier code that does this, but this works.
    // you could also use NSDate to do this, but I'm trying to avoid creating objects.
    totalRead += bytesRead;
    gettimeofday(&tv, NULL);
    if (lastSecond != tv.tv_sec) {
        lastSecond = tv.tv_sec;

        // progressPanel might be passed into this function, or it might be a global variable
        // tailor to meet your needs. might need to wrap in an autorelease pool. 
        [progressPanel updateProgressForTotalBytesRead:totalRead];
    }
}


Personally, that's what I'd do. The C API is really good at that unixy stuff; I don't mind mixing it with ObjC when appropriate.

-- MikeTrent

----

Any time I do [filehandle read] the system creates a new data object? If it is so, of course direct read should be used. Can I read() and write() to a fileHandle?

Do you think polling the time and comparing is will be more efficient than using a timer that fires every second? Probably the timer code is well optimised. 

-- NirSoffer

----

Yes using one of the read* methods of an NSFileHandle will return a NSData object. 

You can get the file descriptor (which I guess you meant by "fileHandle" to be used with read() + write()) by using - (int)fileDescriptor from NSFileHandle.

Using time() and one comparison will be faster than using NSTimer because that NSTimer-object has to be initialised and will be included the your main run loop of your application.
As always: If you depend on code to be really fast, try avoid using objects. But don't overdo it, either :-)

-- KayRoepke

Actually, I don't know if a timer would be more efficient or not. The timer is basically going to block until the appointed time, rather than poll. Since you're only creating one timer (aren't you?) I wouldn't really worry about the initialization cost. The thing with the timer is, though, you have to return time to the event loop in order for your timer to fire. That may not be appropriate all the time. If you're doing this in a single threaded application, you're going to need to give time to the event loop in order for the user to "cancel" and do other operations, so perhaps the timer idea is good after all. 

(Or you could also use -[NSObject performSelector:withObject:afterDelay:] instead of creating a timer, if you were really paranoid about creating a timer object, but really I think the timer is a lot more convenient. Still, I thought I'd mention it.)

But if you're doing this in a separate thread, you may choose to just poll gettimeofday() so you don't have to give time back to other things. Polling gettimeofday() doesn't seem to be that expensive. I definitely use gettimeofday() and not time() so I can support sub-second timing intervals (like twice a second). I actually have a little function I use in a variety of programs that returns the time of day as a double:

    
double GetTimeOfDay(void)
{
    struct timeval tv;

    gettimeofday(&tv, NULL);

    return (double)tv.tv_sec + (double)tv.tv_usec / 1000000.0;
}


This makes testing for arbitrary time intervals a lot easier. 

So regarding timer vs gettimeofday() I think which ever approach you're most comfortable with is fine. -- MikeTrent

