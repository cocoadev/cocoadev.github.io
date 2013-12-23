---
layout: page
title: NSDictionaryInAnNSDictionary
---

I need to create a new dictionary object from a dictionary in an existing dictionary object.

For example, the com.apple.PowerManagement.plist file has a dictionary object at the root. It also has dictionary objects underneath the root dictionary. I'd like to access those dictionaries and read the key/value pairs from them.

----

You'd do well to read the documentation. This is well-covered. If the plist file that you read in as a dictionary has another dictionary called "SomePowerSettings", "SomePowerSettings" is the key. The -objectForKey:@"SomePowerSettings" could be a dictionary or an array (or a number or a string ...). It's up to you to look at the plist file and figure out what is stored under "SomePowerSettings" and make the rright object.

    
// Assumes "plistDictionary" exists and is filled with the plist referenced above
// Also assumes "SomePowerSettings" key exists and stores a dictionary

NSDictionary * spSettings = [plistDictionary objectForKey:@"SomePowerSettings"];

// ... do with it what you will.


It's also worth noting that changing stuff in the power management plist file and re-saving it **WILL NOT** change the power settings. You need to talk directly to the correct preferences domain.

