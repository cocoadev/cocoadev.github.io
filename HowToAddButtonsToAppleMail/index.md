---
layout: page
title: HowToAddButtonsToAppleMail
---

I'm looking to create a plugin for Mail.app that will give users access to some new web services directly from the message window.

Ideally, I'd like to add a toolbar that appears above every message window (and view).  

Other ideas I have had are:
- Adding some menu items to the contextual menu that appears when you click on a given email address (sender)
- Adding some UI elements next to the place where the picture of a sender appears in the MessageHeaderView
- Creating a floating window/toolbar that the user can position near Mail.app

Does anyone have any experience doing this kind of extension to Mail.app?  I'm only interested in doing this for Tiger right now (Leopard, of course, eventually).

Any sample code for a Mail plugin that does something like this would be appreciated.

----
At the risk of being obvious, have you seen MailPlugin?

----
Yes, I have, and it has been helpful.  I can now build a Mail Bundle and get it loaded by Mail.app.  I also now have examples of how to add new menu commands for the top menus, as well as adding Preference panes, and key event triggers.

What I can't seem to find is an example of a plug-in adding GUI controls to the interface of the Mail.app.  I also can't seem to find where the resource definition is for the contextual menu that appears when you click on the "sender" email address in the MailHeaderViewer.

I was hoping there was some sort of runtime tool out there where I can "sniff" the live resources and objects at runtime to get resource names and message selectors, etc.

----
Not sure how much it'll help, but the primary "runtime tool to sniff live resources and objects" is FScriptAnywhere. You don't need to know FScript to use it, since there's a reasonably nice GUI object browser anyway. --JediKnil

