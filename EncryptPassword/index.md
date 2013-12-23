---
layout: page
title: EncryptPassword
---

Call me a newbie, but I can't find a good way to encrypt a password, save it to disk, read it from disk, and decrypt it. I'd like to save it in a plist file, if possible. Is there a wrapper or something for doing this?

Thanks.

Make yourself use the Keychain. It's C only and not very pleasant to work with, but it pays off.

----
Hmm, well, here's what I want to do more specifically: I need to run a privileged task, but I don't want to use authorization services because this is a task I run a lot, and it would be annoying if the user had to keep entering their password. So I want to encrypt the user's password and save it to disk. Also, the task I need to run is "wakein", to wake up the computer from sleep. So I don't think there would be a problem with co-workers or something activating the task without the owner's consent.

----

Don't. If you automatically authenticate your helper application, then an attacker can replace said application and have it execute with no chance for a user to recognize it as modified. Also, storing the user's password, even encrypted, weakens the entire system. All somebody then has to do is look at the assembly for your application to decrypt the stored password and voil&agrave;, the attacker has the user's password and can do anything the user can do.

----

Oh, okay. I guess I'll just use Authentication Services then. You can tell I'm not very experienced with security. :p

Thanks for the help.

