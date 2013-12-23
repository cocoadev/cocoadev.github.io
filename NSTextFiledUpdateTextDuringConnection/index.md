---
layout: page
title: NSTextFiledUpdateTextDuringConnection
---

Ok, I have a modal sheet which is displayed when the user of my application clicks "upload". Inside the modal sheet I have an NSProgressIndicator, which uses [progressIndicator setUsesThreadedAnimation:YES] and also a TextField, which I want to have display the current status of the connection to the ftp server, which is being run in the background. However, whenever I try to update the NSTextField, it's udated contents are not displayed. I update whenever a return string from the ftp server returns something (Connection established, etc). Do I have to put this updating of the NSTextField into a seperate thread? If so how? As The NSTextField has to know where abouts in the Main thread the program is, so it displays the appropriate message. Thanks - bk

