---
layout: page
title: NSPredicateEditor
---

*I would like to use NSPredicateEditor from IB. I tried to read the Docs from Apple but I�m lost. How do I setup a NSPredicateEditor and its NSPredicateEditorRowTemplate in IB?
Thanks*

I just found the sample project, I did searched for NSPredicateEditor and Spotlight brought up nothing useful. Now tried PredicateEditor and found what I needed for now. 
GS

-------

If you put custom views or components inside the NSPredicateEditorRowTemplate then they must implement the following in the custom component:

objectValue
setObjectValue:

Furthermore, you must call:
 [self sendAction:[self action] to:[self target]];

in the custom component, because the NSPredicateEditor will register itself with the components to discover changes when the user changes the state of the component. This implies you will presumably need to implement these also in the component:

target
setTarget:
action
setAction:

The NSPredicateEditor does various merging of the states of all the Row Templates (seemingly more than it needs to, which may be a bug). Failure to implement all of the above in a custom view is likely to result in you not being able to set or get values from the components.

