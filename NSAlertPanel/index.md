---
layout: page
title: NSAlertPanel
---

    
NSAlertPanel.beginAlertSheet(
	String title, 
	String defaultButton, 
	String alternateButton, 
	String otherButton, 
	NSWindow docWindow, 
	Object modalDelegate, 
	NSSelector willEndSelector, 
	NSSelector didEndSelector, 
	Object contextInfo, 
	String msg);

The two selectors must have the following signature:
    
public void willEndAlert(NSWindow sheet, int returnCode, Object contextInfo)
public void didEndAlert(NSWindow sheet, int returnCode, Object contextInfo)

Return codes are statics in NSAlertPanel
    
    public static final int DefaultReturn = 1;
    public static final int AlternateReturn = 0;
    public static final int OtherReturn = -1;
    public static final int ErrorReturn = -2;


Example:
    
public void willEndAlert(NSWindow sheet, int returnCode, Object contextInfo) {
	switch (returnCode) {
	case NSAlertPanel.DefaultReturn:
		System.out.println("modalDelegate.willEndAlert() Save");
		break;
	case NSAlertPanel.AlternateReturn:
		System.out.println("modalDelegate.willEndAlert() Don't Save");
		break;
	case NSAlertPanel.OtherReturn:
		System.out.println("modalDelegate.willEndAlert() Cancel");
		break;
	default:
		System.out.println("modalDelegate.willEndAlert() Error " + returnCode);
	}
}


FrancoisFrisch

