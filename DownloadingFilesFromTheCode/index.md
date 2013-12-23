---
layout: page
title: DownloadingFilesFromTheCode
---

 I want my program be able to download new plugins from a server. I've got the idea to know what plugins that the client doesn't have are in the server, got it from cocoadevcentral's "Internet version checking" tutorial. Now i must download the files, but i don't want the user to have to open any webpage and select the desired files; i want the download to start automatically, but i don't know how. It is possible to use FTP to make this? Is there any other way?

Thanks in advance

-Angel David Puerto

----


Either you do this:
    
[[NSWorkspace sharedWorkspace] openURL:[NSURL URLWithString:@"http://www.example.com/exampleplugin.tgz"]];

Which will open Safari (or the default browser) and start downloading that file. You will probably have no idea of where the file is downloaded (if it is to the Desktop or to some download folder, that depends on the users settings, and if they didn't use "download to...").

Or you do the download yourself, as described in DownloadingFiles, I would recommend that. Then you can install the plugin from within the program while your're at it.

--TheoHultberg/Iconara

----

Well, i made it like in DownloadingFiles, but i found a problem: my plugins are folders and what I get is a file that cannot be loaded from the program.
Now i've two ideas:
     1) Find a way to download the entire folder. I don't know how, so any help will be welcome.
     2) Store the folder compressed like .zip or .sit. In such a case, i want the folder to be uncompressed automatically, and i don't know how.

I prefer the first option if it's possible. 

Do you know how to download a folder or how to uncompress files automatically?

Thanks in advance.   -Angel Puerto

----

I believe that if you use WebKit, the files will be uncompressed automatically. -- Jacob

----

You can download the archive to a temp directory, then use a call to gz, tar or whatever via an NSTask from your app.  You just pass it the path to the archive and where you want it to uncompress to.  

you can get a temp directory with: [[NSWorkspace sharedWorkspace] temporaryDirectory];

EcumeDesJours

----

I believe     [[NSWorkspace sharedWorkspace] temporaryDirectory]; is a category method from the code at karelia.com... You can just use the NSTemporaryDirectory() function instead.



----

my bad.  i always forget that certain things are not just there.  For instance I can't live without [NSString containsString:] but sometimes I forget when recommending it to others that it is not part of Appkit...

EcumeDesJours

