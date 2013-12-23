---
layout: page
title: CannotAllowLimitedUserToUseMyApplication
---



Why cannot we allow limited user to use my application ?

In System Preference > Accounts > Limitations > Some Limits/Simple Finder > "This user can only use these applications:", sometimes we cannot turn "Allow" checkbox on persistently about a certian application.

This problem is caused by lack of the CFBundleIdentifier entry in Info.plist of the application. (Many Java application causes this problem, because this entry does not exist in Java application's Info.plist created with Xcode / Project Builder's wizard.)

The solution is adding one into Info.plist like following:

    
<key>CFBundleIdentifier</key>
<string>com.yourCompany.applicationName</string>

