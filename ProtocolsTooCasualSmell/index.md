---
layout: page
title: ProtocolsTooCasualSmell
---



There are two kinds of protocols, formal and informal. The problem is, it's easy to have too many informal protocols; you can end up polluting the method namespace for any objects inheriting from NSObject.

I would guess that this is why Apple usually has the informal protocols prepended with the object type, as in -tableView:writeRows:toPasteboard:

Another reason is that one object could control several other objects through protocol conformance, thus the selector needs to know which of the possible many objects actually sent the message -- it can also often result in omitting an instance variable for the object controlled, since it will only be touched in the protocol methods.

So, be careful when you're making InformalProtocols, especially in library code.

