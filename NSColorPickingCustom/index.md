---
layout: page
title: NSColorPickingCustom
---

Has anyone implemented an NSColorPicker subclass using NSColorPickingCustom?

I'm in the process, reviewing documentation here: file://localhost/Developer/Documentation/Cocoa/TasksAndConcepts/ProgrammingTopics/DrawColor/Tasks/AddingColorPickers.html

I am currently noticing the following odd behaviour with my color picker:

1) The tool tip that appears over the icon for my picker in the color panel is the same as whatever picker appears before mine. I have all plist entries as far as I've been able to tell but not sure why this would occur.

2) As soon as I click away to another picker my view is still displayed, underneath the new view. It's unresponsive to clicks but it is there. I do not seem to be getting any message to hide it before this occurs so I don't think *I* am missing a message.

3) I'm not sure from the docs if I should be defining my own unique mode/mask or just support RGB (for example). The docs seem to indicate I should use a unique value but there is no way to ensure uniqueness across other developers as far as I can tell.

It's getting there...

- JoeZobkiw

