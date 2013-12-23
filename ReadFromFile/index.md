---
layout: page
title: ReadFromFile
---

    
<key>cubes</key>
		<array>
			<string>bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb</string>
			<string>bbaaabaaaaaaaaaaaaaaaaaaaaaaaabb</string>
			<string>bbaaabaaaaaaaaaaaaaaaaaaaaaaaabb</string>
			<string>bbaaaabaaaaaaaaaaaaaaaaaaaaaaabb</string>
			<string>bbaaaaabaaaaaaaaaaaaaaaaaaaaaabb</string>
			<string>bbaaaaaaaaaaaaaaaaaaaaaaaaaaaabb</string>
			<string>bbaaaaabaaaaaaaaaaaaabbbbbaaaabb</string>
			<string>bbaaaaaabaaaaaaaaaaabaaaabaaaabb</string>
			<string>bbaaaaaaabaaaaaaaaabaaaaabaaaabb</string>
			<string>bbaaaaaaaabaabaaaabaaaaaabaaaabb</string>
			<string>bbaaaaaaaabbabbaaabaaaaabaaaaabb</string>
			<string>bbaaaaaaaaaabbbaaabaaabbaaaaaabb</string>
			<string>bbaaaaaaaaaaaabbbbbbbbbaaaaaaabb</string>
			<string>bbaaaaaaaaaaaaaaabaaaaaaaaaaaabb</string>
			<string>bbaaaaaaaaaaaaaaaabaaaaaaaaaaabb</string>
			<string>bbaaaaaaaaaaaaaaaabaaaaaaaaaaabb</string>
			<string>bbaaaaabbbbbabbbbabbaaaaaaaaaabb</string>
			<string>bbaaaaaaaaaaaaaaaaabbbaaaaaaaabb</string>
			<string>bbaaaaabbbaaaaaaaaaaabbaaaaaaabb</string>
			<string>bbaaaabaabbbaaaaaaabbbbbaaaaaabb</string>
			<string>bbbbbbaaaaabbbbbbbbaaaaaaaaaaabb</string>
			<string>bbaabbbbbbbbbbbbbbbaaaaaaaaaaabb</string>
			<string>bbaaaaaaaaaaaaaaaabbbbbbbaaaaabb</string>
			<string>bbaaaaaaaaaaaaaaaaaaaaaaaaaaaabb</string>
			<string>bbaaaaaaaaaaaaaaaaaaaaaaaaaaaabb</string>
			<string>bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb</string>
		</array>

How do I read this from file and return a and b as different objects using Cocoa/OpenGL.
If it is a tricky question useful links will also help!
----
What do you mean by "different objects"? You can read the array of strings easily (the file looks like XmlPropertyLists), but you'll have to process the a's and b's yourself.
----
I mean a=cubes and b=circles!
How do I do that?
Is the question clear enough?
----
Don't get upset.

It seems easy enough. Turn the NSArray of strings into a more useful data structure, like a C-style char[][] array or a KTMatrix. Then, when drawing in the OpenGL view, iterate through the C array or matrix and draw the appropriate shape at the appropriate co-ordinates.

To turn the NSArray of NSString you get from reading this file into a C-style array: first, figure out how big the array should be (*width* times *height* bytes); second, allocate the memory; third, iterate through the NSArray and each character of the NSString, setting the appropriate value in the C array.

To draw the appropriate shape at the appropriate co-ordinates: first, decide the width and height of the spheres and cubes; second, iterate through the C array; third, multiply the array indices by the width and height to get the co-ordinates of the corner of each shape; fourth, draw the shape based on that corner.

If you need the basics of using OpenGL under Cocoa, might I suggest Apple's tutorial at http://developer.apple.com/documentation/GraphicsImaging/Conceptual/OpenGL/chap4/chapter_4_section_1.html ?
----
I don't ment to get upset, thanks for help. If anything else comes to mind, i'll be even more happy.

