---
layout: page
title: DirectParametersAsValues
---



An AppleScript command's target depends on its direct parameter. The direct parameter can be a *constant* or an *application-provided object*. A parameter like     third word of "do re mi fa" is a constant, but a parameter like     name of application is an application-provided object, though they are both string values. If the command's direct parameter is a constant, then the command's target will be the nearest enclosing tell block, but if it is an application-provided object, it will be that object.

As an example, say you implement a "send" command whose direct parameter should be a string but whose target should be a "network connection" class. In the script     tell network connection 1 to send "Hello" the target of the send command will be *network connection 1*, the enclosing tell block, because "Hello" is a constant. However, in the script     tell network connection 1 to send name of application the target of the send command will be *name of application*, not *network connection 1*, because *name of application* is an application-provided object, not a constant.

You can see the problem. In the second script, the send command won't know which network connection the message should be sent out on.

A solution is possible, but not by default. Apple Events can have a *subject* attribute, accessed by the attribute key     'subj'. Normally, this attribute only shows up when the direct parameter is a constant, in which case it is set to the nearest tell block. But if the direct parameter is an application-provided object, the nearest tell block is ignored and this attribute doesn't appear.

You can tell AppleScript to always provide the subject attribute regardless. You can then rely on the subject attribute, or perhaps an optional "to" parameter of the command, to determine the real target. I say "real target", because CocoaScripting will think the direct parameter is the real target.

To force AppleScript to always include the subject attribute, add a     'scsz' Resource Manager resource to your application. The resource should have the     alwaysSendSubject bit set. Here is a .r file that can do this, to be included in a Resource Manager build phase:
    
#include <Carbon/Carbon.r>

resource 'scsz' (0, "Scripting Size", purgeable) {
    dontLaunchToGetTerminology,
    findAppBySignature,
    alwaysSendSubject,
    reserved, reserved, reserved, reserved,
    reserved, reserved, reserved, reserved,
    reserved, reserved, reserved, reserved,
    reserved,
    minStackSize,
    preferredStackSize,
    maxStackSize,
    minHeapSize,
    preferredHeapSize,
    maxHeapSize
};


You will need to be able to evaluate the     'subj' attribute. Here is a category function on **NSScriptCommand** that can do it:
    
- (id) subjectsSpecifier
{
    id objects = nil;
    NSAppleEventDescriptor *subjDesc = self appleEvent] attributeDescriptorForKeyword: 'subj'];
    if (subjDesc)
    {
        [[NSScriptObjectSpecifier *subjSpec = [NSScriptObjectSpecifier _objectSpecifierFromDescriptor: subjDesc
            inCommandConstructionContext: nil];
        objects = [subjSpec objectsByEvaluatingSpecifier];
    }
    return objects;
}


If the scripter happens to pass in an application-provided object as the direct parameter, you'll need to evaluate it, because the NSScriptCommand's **directParameter** method won't. Here is another category function:
    
- (id) evaluatedDirectParameters
{
    id param = [self directParameter];
    if ([param isKindOfClass: [NSScriptObjectSpecifier class]])
    {
        NSScriptObjectSpecifier *spec = (NSScriptObjectSpecifier *)param;
        id container = spec containerSpecifier] objectsByEvaluatingSpecifier];
        param = [spec objectsByEvaluatingWithContainers: container];
    }
    return param;
}


Finally, you should add the appropriate     <responds-to> tag and the     <cocoa method=""/> tag to [[NSCoreSuite's "item" class. Remember that CocoaScripting will not know that the direct parameter can't be used to determine the target of this command. Adding the command to the "item" class makes *any* direct parameter a valid target for the command, since all AppleScript classes inherit from "item".

�DustinVoss

Updated the "subjectsSpecifier" code, because I found that calling _objectSpecifierFromDescriptor with a nil argument set an error flag in the AppleEvent currently being handled (tested on Leopard). This was causing Colloquy to return an error when I was sending AppleEvents to it.

�DaveMacLachlan

On Leopard, you can use the newly-public method +[NSScriptObjectSpecifier objectSpecifierWithDescriptor:] instead of _objectSpecifierFromDescriptor. It still fails and sets an error on your script execution context if you give it nil or @'null', though.

--WimLewis

