---
layout: page
title: PluginAPIDesign
---



I'm developing a plugin API for my document-based app. I've never had to do one before, and I want to do it right. My plugins are loadable bundles which implement a FormalProtocol to provide information about themselves to my app, and my documents implement a document protocol that the plugin can use to obtain the document's data. Is this a good design? It's working well so far, but there could be demons lurking just around the corner...

Apple has some pretty good documentation on adding a plugin architecture to your app at [http://developer.apple.com/documentation/Cocoa/Conceptual/LoadingCode/index.html]. On the 'Plugin Architectures' page there it says to *"Limit direct access by plug-ins to your application code and data. Do not provide the plug-in with pointers to your application controller objects, application data, or other information that could be accidentally misused or intentionally abused."*

Is this such a big deal? No matter what I give or don't give to the plugin through the API, it can always obtain pointers to my shared application object with     NSApp, my app preferences with     NSUserDefaults, my document objects with     [[NSDocumentController sharedDocumentController] documents]... in short, an evil plugin could wreak havoc no matter what I do to try and prevent it.

I'd love to get some input and advice from people that have done this before!

----

I think it is more so for the plug-in developers with good intentions. Nothing is going to stop them from wreaking havoc on your app. Plug-in developers will make use of everything they have access to. Therefore, only give the plug-in developer access to what he needs - no more - because he will use whatever else to "add features" that your application was not designed for. Don't just hand him a pointer to your center controller for the entire application, instead, design classes with limited abilities for the plug-in developer to communicate to. Sure he can take over your entire application by grabbing NSApp, but it is more difficult for him to change a little thing buried inside the app.

This technique has several other advantages as well. It simplifies the plug-in development and gives you more flexibility in changing something in the future without breaking plug-ins. However, there is certainly a balance. You do not want to restrict the plug-in developer so much that it diminishes his creativity.

-- RyanBates

----

try NSProtocolChecker.
if you pass a NSProtocolChecker to the plugin you can controlle how much and which methods the plugin can use and you protect the secret methods.  from being used...
so with this method you can pass your MainController (wraped in a NSProtocolChecker and masked by a protocol) to a plugin.

-- SimonAndreasMenke

----

Isn't having the ability to "add features your application was not designed for" the primary reason to even have a plugin api?

----

Well, sort of.  Think of photoshop plugins.  Filters? Yes. Make the machine oink when you click on the paint brush tool?  Not so much.  You might want to regulate what kinds of additions plugins can make.

