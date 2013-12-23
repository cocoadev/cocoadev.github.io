---
layout: page
title: CocoaInsecurity
---

see also

ProductSecurityStrategies

MakingSecureRegistrationCodes

SoftwareSerializationPiracyDiscussion

There is an inherent insecurity in all Cocoa applications, simply because of the nature of the ObjectiveC Runtime and the organization of MachO executables (what with name and symbol tables embedded right into them, able to be accessed easily with programs such as nm, otool, and class-dump). This "insecurity" is a great advantage when developing an InputManager for Apple programs such as iChat to enhance functionality, but it's terrible for shareware developers who do not want their http://goo.gl/OeSCu program "hijacked" or taken over by an InputManager or other bundle/software of that type, as this could lead to a very easy crack or workaround to their licensing and protection scheme.

Recently, while examining lists of illegal serial numbers available for applications, to see which ones were uncrackable, I came across something quite interesting: almost none of the recent Omni apps had any serial numbers or "cracks" available for them. Somehow, the brilliant Omni Group had found a way to keep Mac-hackers from taking over their program, or easily discovering their licensing scheme. I decided to look into this matter and try to find out how the Omni Group had creating this unbreakable licensing scheme. It had to be more than a complex algorithm.

So I downloaded the most recent version of OmniGraffle and started digging around in the class-dump of OG's executable. What I found very interesting was that there seemed to be nothing in the plethora of complex classes that make up OG regarding its registration scheme. I decided to try loading the program into gdb, and examining what happened when I clicked the button in OG to add a license (even though there was no model class about the reg scheme in the class-dump, there still was a controller class for the licenses window). I stepped into a function that was supposedly the function responsible for validating licenses. However, when I called up a backtrace from within gdb, I found that I was no longer in a Cocoa function, but in a C function! I killed gdb's process and starting using otool and nm (two command line tools that ship with OS X, and are used for examine the MachO tables in binary executables) to search for any mention of this function (which I had gotten the name from using Sampler). I wanted to see if Omni had defined this function and its class in the actual executable, or had maybe put it into one of the many plugins and frameworks embedded in OG. I found that this function WAS a locally-defined symbol in the executable. I stuck the program back into Sampler, and used different settings than the first time. I was able to find a strange class that seemed to define a "runtime license" in the lengthy backtrace of the many threads in OG that Sampler reported. Now remember, this class was in no way defined in the class-dump of the executable.

I set to work attempting to create an InputManager that would hijack this mystery class so I could see what all of its instance variables and functions were by using scary, low-level, ObjC runtime functions. However, it seems that since this class was somehow not defined in the executable, it was not possible for me to take over in an InputManager. So here was the reason no one has been able to crack OmniGraffle! There is simply no way to gain access to the classes and functions responsible for validating and enabling all of the features in the program.

Now, you are probably wondering whether I am trying to expose the Omni Group's licensing scheme publicly, or if I am up to something else. Let me assure you that I have the utmost respect for the Omni Group (especially after this endeavor of mine), and would not want to jeopardize their programs' protection schemes. What I am trying to do is turn the attention of the Cocoa community to the fact that, even though the ObjC runtime is insecure by its very nature of allowing limitless possibilities for programs on the Mac, there are ways to prevent hackers from taking advantage of this fact, causing losses for you.

Arm yourself with this information! If you are developing a shareware application in Cocoa, and earning money from it is important to you or your company, then remember these key points when designing your registration model:


* Avoid the use of ObjC functions when writing your core protection scheme functions.
* Avoid the use of blatantly specific names for classes that involving protecting your application, such as RegistrationClass or ProtectionScheme.
* If at all possible, create your reg classes on-the-fly instead of at design time (which I think the Omni Group might have done, but I'm not sure).
* When compiling your final project, be very, very sure that you uncheck the checkboxes for generating debugging symbols, profiling information, etc. in Project Builder. Also be sure you don't compile with -O0 as your optimization level, because even then it would be easy to find out how your functions work by using a debugger or some other kind of application.
* And most importantly: **STRIP YOUR FINAL EXECUTABLE OF ALL SYMBOLS BEFORE PACKAGING** This is done using the UNIX program called *strip* which comes with OS X. To view the man page, open the Terminal and type     man strip. You can also use ProjectBuilder to view the man page in prettier style. Stripping all non-global and non-external symbols (which include your application's references to the AppKit and FoundationKit, as well as to any other externally linked frameworks you may use) *should* keep prying eyes from class-dumping your program's executable to file to view all of your classes and methods.


So in conclusion, I'd like to tip my hat to the Omni Group for their ingenious protection scheme of their applications, and for recognizing the insecurity in the ObjC runtime before the rest of us did. Good luck writing all of your applications, everyone, and remember the points I outlined for you above so you get the maximum number of sales for your product. However, always keep in mind the grim truth that a determined hacker will almost always find a way to circumvent your licensing and protection scheme, no matter to what lengths you go to protect your program from such break-ins.

-- MarcWeil

----

I think no matter how far this conversation goes, we will end up with the same unequivocal point. If someone wants to break into your app and allow it so they do not have to pay a shareware/commercial license and they have the correct skills then they will do it, its that simple. -- MatPeterson

----

It doesn't matter how secure your algorithm is, or how sneaky your blacklist-checking methods are because all of those functions can easily be turned off or overridden using an ObjC runtime exploit. However, SecurityThroughStrongEncryption, championed by AllanOdgaard (among others), would be very secure if we didn't have to worry about losing control of our protection scheme methods.
--MarcWeil

----

How about peppering all of your sensitive source with conditionals that check for a custom serial number. This would require providing custom builds for every piece of software you sell, but processors are getting fast enough to compile custom builds to your hearts content. Your application could also self assemble, meaning distribute a generic application bundle with resources that never change and just move your custom frameworks or bundles from a separate package. This would reduce the amount of uploads to your web site and reduce the amount of overhead caused by the added compile time. If you already follow good MCV practices you would only need to build custom controllers (NSBundles can be really handy here). An easy way to pepper your source with custom conditionals is to define a serial number in one header and import away!! It's one thing to search and hack one function, but to search and hack 1000+ custom conditionals? If you got really sneaky, you could write a source editor that randomly inserts these conditional statements throughout critical portions of code. These random insertion points could be tempered by restricting the locations to places that have a unique comment tag. You could also define your serial number in 8 bit slices to make it harder to scan. By mixing in some dependency for the particular machine the software is installed on, generating a key that could only be used in a particular environment could help too (haven't thought about this part much though). 

I don't know, these are just ideas... --zootbobblu

----

Very interesting ideas. I'm not sure I completely understand it, though. If you had to build a custom executable  for everyone, then how would people download it from the web site freely? Maybe I just misunderstood the ideas, but nevertheless, it sounds like a pretty good idea. Maybe a little more explanation about "self assembling applications" and peppering the source code with conditionals, would help to further explain your concept.

However, I personally like the use of machine-dependent keys, and using this key (and the user's name) to generate a serial number. The key would, of course, be unique among all computers which almost guarantees that all of the serial numbers will be different as well. There is only one flaw in this scheme that I can find, however, and that is that you are forced to store the machine's code somewhere on the computer. If someone finds out where this file is, it would be quite easy for them to create a crack for the program that would modify this file with a static machine code so the same serial number (and a given name, of course) could be used on any computer that applied the crack. Of course, this is where blacklisting comes in handy, but the problem with blacklisting is that if the blacklist is stored on the server, people can block the connection with LittleSnitch or another kind of firewall-like app, and therefore still get the program for free. If you store the blacklist locally in the app's bundle and simply add info to it as more versions are released, it would be no problem for someone to find the information and just get rid of it to make it look like an empty blacklist.

-- MarcWeil

----

Aren't fake serials the only real problem here? so as I see it it is enough (and much easier) to make it unlikely for the cracker to be able to generate a fake serial.

As pointed out a dozen times earlier, someone will always be able to hack into your program, after all, the entire source is available (in assembler, though) -- but if someone actually did, he would need to make modifications to the code, get them back to the MachO binary and release this as a crack (is anyone releasing cracks these days?) and would have to repeat this for each new version released...

Lol, yeah... I never mentioned anything about fake serials in my post. The main thing I was talking about was the ability to take over the execution of a program by simply using class-dump and an InputManager. You really don't even (usually) need to access the assembler source of the binary because you already have everything you need available to you from the class-dump. -- MarcWeil

----

If you can isolate the unique functionality of your app into a conservative number of objects, you could separate these objects and build them into a single bundle. Like I was saying about good MCV practices, this shouldn't be a stretch to figure out which objects are worth protecting and which objects are just playing a supporting role. Once you have grouped the objects that do the heavy lifting and organized them into a working bundle, you can focus on this single bundle for custom distribution. I figure that PHP techniques can dynamically create a custom distribution by serving the generic application shell along with a unique distribution of your isolated bundle. Your application shell is a fully functional Cocoa app that launches and is capable of interacting with the end user, but this app doesn't have any useful features until the custom bundle is moved into the app bundle and provided with the correct unique key. The app shell facilitates the self assembly of the final product by asking the main bundle where it is located and moving the custom bundle into the appropriate directory. Maybe you could assemble a custom app with PHP on the server side by having an inventory of unique custom bundles already uploaded and available for PHP to assemble and distribute, but either way your app server still needs to have a pool of custom bundles to provide a dynamic custom distribution. I'm sure a there are limitless ways to accomplish this (like using a package manager), so please add more if you think of any easier ways to do this.

The part about peppering custom conditionals would be nothing more than adding statements that compares the supplied key to the built in key and corrupting the execution of the app if the condition is false. How you terminate the app is up to you, and the possibilities are endless. Dividing the key into bytes is to prevent someone from just searching for unique words of a specific length in your binary (I'm assuming that when you define constants the compiler doesn't generate a table of these definitions and simply places the values inline and leaves out the definitions). Your custom conditional statement would then only randomly check single bytes of the whole key throughout your critical code. Also, when I said 1000+ custom conditional statements, I didn't mean load your code with so many checks that it affects performance, just enough to make it not worth the time and energy for someone to hack. How this could be implemented is another story, but it wouldn't be difficult to create an editor to do this automatically, plus building custom bundles from the command line is a piece of cake (can someone say shell script!!). I'm pretty good at writing parsers, so this is a none issue for me, but if there is enough interest in this type of protection scheme I would be willing to contribute a tool to automate the process. 

--zootbobbalu

----

Great, I just looked at a list of serial numbers and found codes for my shareware. In addition to what's listed above, here are two mechanisms I'll be adding for my future releases:
 
*Leave the registration-related NSTextFields and NSButtons unconnected in the nib and connect them programmatically to methods with obfuscated names. 
*On second thought, this probably wouldn't help much at all...you could always just run the application and find the control connections in gdb*
*When an incorrect registration code is entered, cause the thread to sleep for a few seconds.


----

Geez, that sucks. Good luck on implementing better protection schemes for your program. Think of it as a challenge! :-\

Zootbobbalu, your method seems quite complex, albeit very well thought out. I'm probably too far along in the coding of my program to attempt to implement the second suggestion you had (about the many conditionals), however I will probably start looking into the first. The way I am doing it right now is to generate a unique machine code (as I talked about in one of my previous posts) for the computer, and then when the user purchases the program, they enter the code into the form and a serial number is generated from this code and their name, and returned to them in their receipt. Then, when they enter their name and serial number into my program, the program generates another serial number using the information they entered, and matches it to the code they've entered. If they match, it's valid. If not, it's invalid. My program also checks against a blacklist located on my site's server for either the name, machine code, or serial that they've entered.

I'm not quite sure how to go about implementing your first suggestion at this point, but hopefully I'll be able to figure it out.

On another note, I think that if we all band together to come up with a great protection scheme, the majority of the Mac shareware community will not have to worry about people pirating their software. C'mon everyone! :-D

-- MarcWeil

----

Ah, okay. My mistake. I do realize that the *major* problem here is serial numbers for apps being posted, however with the same basic hacking philosophy that can be used in an InputManager to take over the program (btw, the InputManager can then be distributed and called a "crack" in a sense, as it operates the same way) as with generating a serial number. For example, if a program contains a function that generates a serial number given a name and then compares the generated serial to the user-entered serial, all a hacker would have to do would be to create an InputManager and override that method, and simply find the correct member variable and print it to the console using NSLog(), and voila! He now has a serial number!

That's why remedying (or trying to work around) these "holes" in the ObjC runtime are so very important. There are a countless number of ways a determined hacker could exploit these weaknesses and gain access to your program without paying, and then find a nice and easy way to distribute the crack or serial number to the masses.

Also, we aren't talking about altering the binary here. We are mostly talking about hijacking the program *as it's running* with tricks such as using NSObject's poseAs: method to pose as the registration class, and then override one of its methods. It would be very, very easy to distribute since the InputManager might only be 40k or something in size.

-- MarcWeil

----
IMHO the best solution is to use public-key crypto as advised by AllanOdgaard (to try to defeat serial generator) combined with checkpoints occuring often in application at very different unrelated places in code (to try to defeat crack/patchs by multiplying patch locations).
 
*Checkpoints should be inline functions or macro (prefered), so the code is duplicated instead of being reused..
*Checkpoints should authenticate the public key used to decrypt serial (a pirate could easily generate another public key to replace your public key and using the corresponding private key in a serial generalor to defeat the system)
*Checkpoints could be activated severals minutes/hours after the launch of the application, so the pirate can think the application is cracked.
*Checkpoints macros could have several variants (to defeat multiples "search & replace")
*All these protection should be transparent (except registration) so legitimate users must not be penalized. I am not a big fan of using computer/os internal id/info to create serial, it can be very annoying to users who upgrade their system (ex: new HD) or who need to reinstall thier system (ex: HD crash). Personnal info should be sufficient to discourage them to give their own serials to other people.


--StephaneBoisson

----

The thing about input managers are, that they are Objective C classes which are loaded when the program is launched, and thus one could overwrite the +(void)load method, and when executed, one would be able to access most Objective C classes of the application, so using some form of introspection one could e.g. find the class named ValidateSerialNumber and swap the method implementation of "- (BOOL)isSerialValid" to always return YES.

The problem is of course, that not everybody designs their program in this way :-)

**Another problem is the relative wonkiness of +load implementations...**

----

Someone just found a perfect way of cracking my app. They simply edited to binary and are distributing it on the web. I used lots of obtuse methods to attempt to break it but it seems they overruled the return value for one of my functions. Easy fix though...use notifications....but they will still crack it again, will just take more time. 

----

... another way to thwart would-be thieves would be to apply simple anti-debugging and anti-reverse engineering to your code...  Apple has even, conveniently enough, provided some simple protections for you.

for example: launch iTunes (or DVD player).  Attempt to attach gdb to the running iTunes/DVD Player process.  Try to launch iTunes or DVD Player from gdb.

here's your hint (from xnu-517/bsd/kern/mach_process.c):

    
        if (uap->req == PT_DENY_ATTACH) {
                if (ISSET(p->p_flag, P_TRACED)) {
                                exit1(p, W_EXITCODE(ENOTSUP, 0), retval);
                                /* drop funnel before we return */
                                thread_funnel_set(kernel_flock, FALSE);
                                thread_exception_return();
                                /* NOTREACHED */
                        }
                SET(p->p_flag, P_NOATTACH);

                return(0);
        }
....
                if (ISSET(t->p_flag, P_NOATTACH)) {
                        psignal(p, SIGSEGV);
                        return (EBUSY);
                }


Now this is still trivial to work around, but still... it's interesting that Apple implemented this little roadblock in their applications to prevent others from reverse engineering them.... those eager for more fun can try launching iTunes for Windows with SoftICE (or windbg, pick your favorite debugger...) running....

----
The ptrace(PT_DENY_ATTACH, ...) is really not effective. It's very simple to write a kext that disables the functionality, ie:
    
int32_t our_ptrace(struct proc *p, struct ptrace_args *uap, register_t *retval)
{

	if (uap->req == PT_DENY_ATTACH)
		return (0);
	else
		return real_ptrace(p, uap, retval);
}


----

There are a number of things one can do that can trigger bugs in other applications, leading you to carefully step through the assembly and see where they went wrong - and fix it. Examples: Working with libSystem, implementing a haxie, or writing a driver.

----

The following article shows how to debug applications which use the PT_DENY_ATTACH ptrace under GDB:
http://steike.com/HowToDebugITunesWithGdb
It must also be borne in mind that, no matter how many registration checks &c. you put into your application, and no matter how complex your serial validation algorithms, every application can be kracked, no matter how hard you try. My best advice would be to spend a little less time fighting a war against piracy, instead focusing on perhaps putting features into your app which will make more legitimate users want to buy it.
Also, here is another interesting and relevant article from Slava of Unsanity:
http://www.unsanity.org/archives/000101.php
-[k|s]

----

I assume that what Zootbobbalu is referring to is including source to at least some part of the program on your website, and having the webserver compile that code, link it into the application, and build a new package for *each* user downloading the application. that's a little time consuming, but for some applications that's probably acceptable.

----

The size of this page is already pushing the limit for this wiki. Opinions vary about the value and effectiveness of serial numbers.

The continuing discussion of software serial number piracy has been relocated to SoftwareSerializationPiracyDiscussion.

