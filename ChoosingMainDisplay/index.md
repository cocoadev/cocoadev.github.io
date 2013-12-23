---
layout: page
title: ChoosingMainDisplay
---

Here's a piece of code to help changing the main display with Quartz Display Services for a 2 displays configuration

    
#include <ApplicationServices/ApplicationServices.h>

#define MAX_DISPLAYS 32

int main (int argc, const char ** argv) {

  CGDirectDisplayID activeDisplays[MAX_DISPLAYS];
  CGDisplayErr err;
  CGDisplayCount displayCount;
  CGDisplayConfigRef config;

  err = CGGetActiveDisplayList(MAX_DISPLAYS, activeDisplays, &displayCount);
  if ( err != kCGErrorSuccess )
  {
    printf("Cannot get displays (%d)\n", err);
    exit(1);
  }
 
  CGBeginDisplayConfiguration(&config);
  CGConfigureDisplayOrigin(config,activeDisplays[1], 0, 0); //Set the second display as the new main display by positionning at 0,0
  CGConfigureDisplayOrigin(config,activeDisplays[0], CGDisplayPixelsWide(activeDisplays[1])+1, 0); //Arrangement of the old main display to the right of the new main display
  CGCompleteDisplayConfiguration(config,kCGConfigureForSession);

  return 0;
}

