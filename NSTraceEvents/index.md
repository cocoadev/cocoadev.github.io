---
layout: page
title: NSTraceEvents
---



If you need to see what events your app is receiving launch the bundled executable from terminal and pass the parameter -NSTraceEvents YES to it. Events will appear in the terminal window. If you want this to persist across launches, add a NSTraceEvents key to your app's defaults database with a YES value. In this case events will go to the console.

(from BookLearningCocoa 1st edition, page 131)

----
You can also add the flag via Xcode - Project -> Edit active Executable ..., then add another command line arg "-NSTraceEvents YES". Later on you can then check or uncheck in the same window as needed.

