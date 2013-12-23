---
layout: page
title: MoreControllerKeys
---

i'm writing my own controller and i would like to change some of the controller keys.
instead of the 'selection' key i would like to see 'connection' 
and i would also like to programaticaly show model-key-paths in IB.
normaly you define them in IB but i want to do it in my palette, inspector or controller class.

----

This seems easy enough. Just use **+ exposeBinding:** and **- unbind:** from the **NSKeyValueBindingCreation** protocol.

----

yes that would work if you want to add a binding like 'value' or 'enable' or 'hide'.
but i cannot change the controller-keys like 'selection' or 'arrangedObjects' which show up inside the 'controller-key' comboBox.

----

If you've tried it, and it doesn't work, then IB might have hard-coded support.

----

i think it is hard-coded :-(

