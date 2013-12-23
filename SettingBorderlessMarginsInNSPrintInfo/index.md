---
layout: page
title: SettingBorderlessMarginsInNSPrintInfo
---



The following article was posted on Apple's printing mailing list by Dan Messing. ( http://lists.apple.com/archives/printing/2007/Jan/msg00000.html )

I have not seen any responses to his question, but I am experiencing the exact same difficulties. Does anyone have any suggestions on how to get around this?

Thanks, in advance, for any suggestions you can offer.

  -- Mark


*
On Thu, 04 Jan 2007 16:28:53 -0600, Dan Messing wrote:

Subject: Setting borderless margins in NSPrintInfo

Hi,

I'm having a problem with setting margins in an NSPrintInfo object. Some printer's borderless sizes are listed in a sub-menu in page setup under a general submenu for that paper size. This makes sense, the paper size is the same, but the printable margins are different. Everything seems to work fine when going through the Page Setup dialog and selecting the borderless option - the margins get properly set to 0. However, when I try to programatically set the paper size and margins to borderless values (the same as the ones listed in the sub-menu), I am unable to set the margins to 0. They seem instead to get set to the regular margins for that size - the same as the name that has been set at the top level of the menu.

I assume that NSPrintInfo is doing some kind of check to make sure that the values are being set to a reasonable value, but the check is going wrong here - it's missing the sub-menu values.

The same occurs if I try to programatically set the name of the paper. A pseudocode example, assuming the printer has two paper sizes, "4x6" and "4x6-borderless", which would both be placed by the page setup dialog in a submenu titled "4x6":

    
NSPrintInfo *sharedInfo = [NSPrintInfo sharedPrintInfo];
[sharedInfo setPaperName: @"4x6-borderless"];
NSString *newPaperName = [sharedInfo papername];
NSLog(@"%@", newPaperName);


The output of which isn't "4x6-borderless" as would be expected, but instead "4x6" - the name of the submenu containing the two items.

The problem happens for both an Epson and an HP printer that I am testing with.

Does anyone have any insight into how I can work around this problem?

Dan

