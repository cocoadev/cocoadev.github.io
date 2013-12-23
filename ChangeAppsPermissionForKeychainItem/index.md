---
layout: page
title: ChangeAppsPermissionForKeychainItem
---



I'm new to Keychain and, in general, the security framework, but I've made a bit of headway recently. I have my app storing the user's password for a particular internet service, using the SecKeychainAddInternetPassword, SecKeychainItemModifyContent, SecKeychainFindInternetPassword functions. When the user enters a new username/password combination, it creates the keychain item, and when they change the password field for an existing username, it modifies the combination. However, it does so without prompting the user for permission. I want to make the user have to log in to keychain in order to modify the password, but since my application created the keychain item, it automatically receives "always allow decryption" status in the Keychain system. 

I need to make it so, when the user wants to modify the password for the first time, they get a keychain prompt, at which time they can set the preferences to Allow Once or Always Allow at their choosing. I can do that by manually removing the "always allow" permission from the Keychain Access program, but I want to do it by code. I've looked through the Keychain methods extensively, and I can't seem to find anything for changing this value. It might be right under my nose, but I fear it's more complicated than I'd like it to be, as I just can't find anything for this purpose.

