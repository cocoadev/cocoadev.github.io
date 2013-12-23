---
layout: page
title: CharacterPaletteData
---

I'm interested in accessing the data that the character palette displays. Does anyone know where this is kept? There's a lot of interesting data on japanese characters in there, such as readings, radicals, and stroke count. Where does it find this data? Is there a central (open!) data file in Mac OS where I could find this data? Or is it a private file in the Character Palette that I'd need to reverse engineer?

Thanks!

DanielPeebles

----
Ok. This might be off-topic now, but I thought there might be a cocoa interface to the data which is why I asked it in the first place. I found it, and it's a group of files inside the /System/Library/Components/CharacterPalette.component/Contents/SharedSupport/CharPaletteServer.app/Contents/Frameworks/CharacterPaletteFramework.framework/Versions/A/Resources folder... the main file is called kanji.db, and is a sqlite database file with a bit of data on each character in it.

