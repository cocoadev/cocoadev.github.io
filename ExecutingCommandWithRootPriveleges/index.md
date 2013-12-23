---
layout: page
title: ExecutingCommandWithRootPriveleges
---

I am creating an application (and perhaps later a preference pane) to control an Apache web server. However, I need the user to have administrator priveleges to (a) start and stop the server and (b) save to the configuration file. However, Apple's Authorization Services documentation seemed very complicated. All I want is a quick and to the point method of executing an NSTask with administrator privileges and saving a text file with administrator privileges.

----

As far as I can see, there is no way doing this using NSTask. You should probably use the function     AuthorizationExecuteWithPrivileges() from the Security framework. Check out http://developer.apple.com/documentation/Security/Reference/authorization_ref/01authref_ref/function_group_5.html for some documentation. Related sample code available at: http://developer.apple.com/documentation/Security/Conceptual/authorization_concepts/03authtasks/chapter_3_section_4.html. Well, this *is* a solution, but means definitely more work than with a Objective-C approach.

PS. I just noticed that there is a related discussion at AuthorizationExecuteWithPrivilegesAndStdout. That might close the gap.

----

You shouldn't use AEWP(). You should have a tool. There are all sorts of reasons why this is. MoreAuthSample should be pretty easy to follow and adapt to your own purposes. -- FinlayDobbie

----

I must confess that I did not find the MoreAuthSample code and the security "instructions" in general "easy to follow" last time I gave it a look.

In my case all I wanted was that when the user loads or saves a file, and he is not allowed to do so, he should be presented with the question of wether to do this as an administrator, and if so, ask for password and perform the load or save operation as an administrator.

Sounds pretty basic to me, but apparently it is not.

As I understood it (sorry, it's been like a year or so since I last looked into it), I should create a helper tool to read/write these "protected" files and then communicate with that tool (using std in/out) -- I should set the u bit (and owner root or whatever) for this helper tool, so that it can actually do its job, and so I should use the security framework to set that bit from the main application (rather than have the main application do the actual work) -- but then comes all the problems of having the helper tool on a writable volume and one that supports unix protection bits.

And the question also arise, should I clear that bit again? surely if he wants to save a protected file in the next session of using my program, he should be authorized again!

Also, having the tool somewhere with the u bit set means that everybody can use it to read/write files? is that not a major security risk?

And finally, I do understand that encapsulating all my code that requires to execute as root into an external tool draws a hard line between the main app and that code, so that I might not by accident (if I am a sloppy/inexperienced programmer?) execute general non-super user stuff as root -- but when my tool does something as simple as reading a file, and my main application *still* needs to run as root to copy that tool to ~/Library/Application Support or /tmp and set the owner/protection bits, and adding the extra complexity of an application data exchange protocol to my helper tool -- then is this not much worse than simply doing (in pseudo code):
    
if(fileAttrs.owner != currentOwner)
{
   if(RunAlertPanel(@"Do you want to authenticate?", ...) == OKAY)
   {
      if(auth = ObtainSuperUserPrivileges())
      {
         ReadFile();
         ReleaseSuperUserPrivileges(auth);
      }
   }
}


----

I tried (but failed) to decide on an existing location to refactor (or simply orphan) this threaded discussion. My suggested candidate is

CocoaAppsWithAdminstratorPrivs

Others I considered:

RunTaskAsAdministrator - seems inappropriate according to above discussion

AuthorizationExecuteWithPrivilegesAndStdout seems less desirable since discussion here discourages using that function

