---
layout: page
title: UndefinedSymbolErrorWhenBuilding
---

When I build my project I get the following error. What is causing this and how do I fix? The function can be found in General/GISGeometry, but is not being seen in General/GISView.

ld: Undefined symbols:
_GISUnionRect
/Users/priggs/General/GISView/build/General/GISView.build/Debug/General/GISViewFramework.build/Objects-normal/i386/General/GISView.o reference to undefined _GISUnionRect
/usr/bin/libtool: internal link edit command failed
		ld: Undefined symbols:
		_GISUnionRect
		/Users/priggs/General/GISView/build/General/GISView.build/Debug/General/GISViewFramework.build/Objects-normal/i386/General/GISView.o reference to undefined _GISUnionRect
		/usr/bin/libtool: internal link edit command failed
Build failed (2 errors)

Download the project here:
http://www.warnercnr.colostate.edu/~priggs/General/GISView.zip
