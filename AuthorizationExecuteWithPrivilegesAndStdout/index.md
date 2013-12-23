---
layout: page
title: AuthorizationExecuteWithPrivilegesAndStdout
---



What's the best way to use AuthorizationExecuteWithPrivileges in a Cocoa application and have access to the stdout of the task?

Specifically, I'm interested in getting the stdout of an asynchronous task. NSFileHandle has the convenient method readInBackgroundAndNotify and I would like to have something similar for AuthorizationExecuteWithPrivileges.

Also is there any Cocoa wrapper for the security for these calls other than BLAuthentication. BLAuthentication is fine and all, but I'm interested in something that doesn't use shared instances and can be handled in a similar fashion as a NSTask.

----

I wrote a small wrapper for one specific program, and it wasn't too hard to make. (It wasn't completely a walk in the park either, though.) AuthorizationExecuteWithPrivileges gives you a     FILE * that you can use to communicate with the task, but it doesn't interface well with a runloop if you want to do async IO. However, you can get the file descriptor of a     FILE * by calling     fileno(), and you can use that descriptor to initialize an instance of NSFileHandle.

----

You shouldn't do this. Instead have your tool repair its setuid bit, then just execute your tool as usual with an NSTask. See MoreAuthSample for an example of this. -- FinlayDobbie

----

No disrespect intended to the authors, but MoreAuthSample is a mess -- a kludge of corefoundation and BSD calls that for all its complexity
manages to be incapable of being used with even the slightest variation from its limited preconceptions -- for example, it absolutely will not
work with a tool that is embedded in a bundle, because it never even considers that possibility. MoreAuthSample needs to be totally
reconsidered, and a version coded (as much as possible) in Cocoa.

Its current lack of utility simply encourages developers to "roll their own" or fall back on the very approach (AEWP) that Apple is trying to discourage.

I would also dispute DTS's notion that using the suid-root technique is somehow more secure than the authorization-execute-with-privileges
technique, as the suid-root scheme has the tool calling _itself_ using AEWP to install and repair itself (which drives a spike through the contention
that authorization-execute-with-privileges is somehow "going away". If it does, so does the suid-root technique.). 

All a would-be black-hat has to do is creatively corrupt or replace the (unprotected) template version of the tool in the parent application bundle
in order to get _their_ version of the tool installed at some point when the application runs the tool. This is true because MoreAuthSample assumes
that the template is legit, and will discard any previously installed tool in favor of the template if the two don't match. The use of MoreAuthSample
as a template only makes exploits _easier_, because it lets the black-hat construct an exploit that can corrupt _any_ app that uses a suid-root tool.
Such an exploit would just browse the various Application folders looking for bundles returning a value for <code>-[NSBundle pathForAuxiliaryExecutable:]</code> and then replace or modifiy the auxiliary executable template. Any system running in
Single User or admin-user modes (the defaults for most Macs) would be easily subject to this exploit.

OTOH, an application that validates its own tool (via a MD5 or some such) should feel perfectly proper in calling the tool with AEWP. 

It would be handy if Apple would expose its <code>-[NSData MD5Digest]</code> method.

Obviously validating the tool-template in the suid-root technique would also be a good idea.

----

You cannot validate your tool using a checksum, since binaries can be changed on the fly due to prebinding and suchlike. Of course, prebinding is going away, so that may not be the case in the future.

I understand what you mean about the approach of MoreAuthSample, but this can be rectified if it's installed with the correct privileges (or at least run once by an admin user after the application is copied into the directory, and repairs itself to the correct privileges). Then you can't have a malicious non-admin user overwrite it.

The main security benefit is that it encourages you to factor out the privileged code from your main application to make it easier to audit the stuff that runs as root. There have been instances where using high-level frameworks in root applications have caused problems - for example the Recent Applications/NetInfo Manager (which is setuid root for "historical reasons") exploit from the ~10.0-10.1 era (launch terminal, quit terminal, launch NIM, go Apple->Recent Items->Terminal, boom! root shell!).

Obviously they can't just get rid of AEWP(), but I don't think you give them enough credit. Also, I haven't looked at MoreAuthSample in detail, but I was able to quite easily adapt AuthSample to work in ThemeChanger, and MoreAuthSample certainly seems to be a higher level abstraction than that.

-- FinlayDobbie

----

I just spent some quality time today with MoreAuthSample.  It does do a lot of heavy lifting for you, and does illustrate a way to implement the self-repairing-setuid-helper-tool approach, covering a lot of wiggly-niggly issues that one should be concerned with.  It is unfortunate that it is so heavily ensconced in the MoreIsBetter routines, which have that old-time Carbon/Toolbox feel.   Still, a lot of that stuff is useful, like the utility routines in MoreCFQ.[ch].  Very handy for working with CoreFoundation data types.

MoreAuthSample was last updated in 2003, which is fine, I guess, since it is only sample code.  If one wanted it updated, one could burn a ADC tech support ticket and ask DTS to do it.  The issues it addresses haven't changed since then, and the code comments are very good.  It would be nice to see a more bare-bones (i.e. sans MoreIsBetter) sample implementation of this task.


-- BillGarrison

