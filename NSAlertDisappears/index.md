---
layout: page
title: NSAlertDisappears
---



I've encountered an odd problem with an alert panel in a certain situation: I have my code setup to display an alert (using NSAlert as described in the docs) when a file changes; everything works fine normally (app icon bounces, I click the app and alert is there), except when I have a another window showing in my app (it's not a document window, but the app is document based): when I click the app, I see that the alert is up, but it immediately disappears, and I cannot respond to it (debugging also shows it's not getting dismissed by other means either), and I don't understand why this happens, and I've found no mention of alerts 'disappearing' elsewhere either.

Please note that this is not a case of trying to run multiple sheets at the same time; the alert, and the windows are just that, windows (or panel for the alert) not sheets, and nothing else other than the alert itself is being run as a modal window (though it does work fine even if there is a modal sheet in my tests).

Any insights on what might be causing this?

----
I've found in the past that exceptions will make things "vanish"; it's a pretty obscure cause, but it might be what is happening here.

