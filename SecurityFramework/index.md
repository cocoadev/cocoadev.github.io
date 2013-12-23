---
layout: page
title: SecurityFramework
---



SecurityFramework can do several things, which all fall under the header of "Security" and the CDSA (Common Data Security Architecture).

Firstly, Authorization Services manages authorization and authentication for performing privileged operations. This is seen, for example, in the SystemPreferences application with the lock in the bottom-left hand corner of system-wide preferences, in the Installer and Software Update applications, and many others. You can use Authorization Services to minimize the possibility of someone abusing your application of doing accidental damage to important resources.

If you are going to use Authorization Services, I STRONGLY URGE you to read the Apple documentation IN FULL. The conceptual overview is at http://developer.apple.com/documentation/Security/Conceptual/authorization_concepts/index.html and the reference is at http://developer.apple.com/documentation/Security/Reference/authorization_ref/index.html -- you should read and re-read the overview until you fully grok what's expected of you. There is a lot of code out there which encourages you to do things incorrectly. I also advise you to spend time poking through http://developer.apple.com/samplecode/Sample_Code/Security/AuthSample.htm and http://developer.apple.com/samplecode/Sample_Code/Security/MoreAuthSample.htm which will probably help tremendously. See also CocoaAppsWithAdminstratorPrivs.

Secondly, you can do secure network transport over SSL v3.0 and TLS v1.0. Take a look at http://developer.apple.com/documentation/Security/Reference/secureTransportRef/index.html and http://developer.apple.com/samplecode/Sample_Code/Security/SSLSample.htm

Thirdly, you can manage certificates, keys, trust policies and all that jazz. See http://developer.apple.com/documentation/Security/Reference/certifkeytrustservices/index.html

Fourthly, there is a low-level Keychain management API. http://developer.apple.com/documentation/Security/Reference/keychainservices/keychainServRef/Introduction.html

Finally, you can do cryptography. See http://developer.apple.com/samplecode/Sample_Code/Security/CryptoSample.htm

You should also note that SecurityFramework is opensource as part of Darwin at http://developer.apple.com/darwin/projects/security/ and there is a mailing list at http://lists.apple.com/mailman/listinfo/apple-cdsa

