---
layout: page
title: InstantiateCell
---

Hi

	Does anyone know why one can't instantiate a NSCell in the Classes tab of a nib file in IB ?
	I ask that because I would like to connect the outlet 'dataCell' of an NSTableColumn.

	Zauc

----

Yes. It is because IB is insane. It assumes you could never want to instantiate a cell outside of an NSView.

-- KritTer

----

Do I hear a palette calling my name? -- RobRix

