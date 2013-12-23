---
layout: page
title: OpeningAPlainTextFile
---

My Cocoa application should be able to open any plain text document, even without an extension.
I have for example a file created with MacSOUP which has TEXT for OSType but I can not manage to make it selectable in my openPanel without enabling other files like pdf, tiff, etc.
The file is listed in the finder as a plain text document so I really can not understand why I can not select it in my openPanel from my NSDocument based cocoa application.
I tried various entries in my info.plist file but none of them work. This is my current version.
Any ideas of what I'm doing wrong?

    
	<key>CFBundleDocumentTypes</key>
	<array>
		<dict>
			<key>CFBundleTypeExtensions</key>
			<array>
				<string>txt</string>
			</array>
			<key>CFBundleTypeMIMETypes</key>
			<array>
				<string>text/plain</string>
			</array>
			<key>CFBundleTypeName</key>
			<string>SceneDocument</string>
			<key> CFBundleTypeOSTypes</key>
			<array>
				<string>TEXT</string>
				<string>TXT </string>
			</array>
			<key>CFBundleTypeRole</key>
			<string>Editor</string>
			<key>NSDocumentClass</key>
			<string>SceneDocument</string>
		</dict>
	</array>


----

You should specify a UTI (UniformTypeIdentifier). These should (and probably will) be more commonly used on Mac OS X in the future than OSTypes, although they haven't been given much publicity. Tiger has full support for UTIs. 

In your case, use public.plain-text. That should get things working correctly, at least on 10.4.

-- DustinMacDonald
----
Yes, thanks. 
This is working when I start a new project but it won't work in my existing project.
I'll have to investigate what is wrong.
In any case, this is what I was looking for.

