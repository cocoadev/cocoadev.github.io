---
layout: page
title: NSTimeInterval
---

A Foundation defined type. Always in seconds; yields submillisecond precision over a range of 10,000 years.

    typedef double NSTimeInterval;

[http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/TypesAndConstants/FoundationTypes.html]


----
Whilst NSDateFormatter is great for formatting dates and times, is there something standard and localised for formatting a NSTimeInterval, e.g. to show "5 seconds", or "3:45 minutes", "7:32:03 hours", "1.5 days", etc?

Currently I'm using:
    
    float time = ....;

    const int secsPerMin = 60;
    const int secsPerHour = secsPerMin * 60;
    const int secsPerDay = 24 * secsPerHour;
    const char *timeSep = ":"; //@TODO localise...
    const char *secsName = "secs";
    const char *minsName = "mins";
    const char *hrsName = "hrs";
    const char *daysName = "days";

    int hrs = time/secsPerHour;
    if(hrs > 36) return [NSString stringWithFormat:@"%0.2f %s", time/secsPerDay, daysName];
    if(time < 60.0) return [NSString stringWithFormat:@"%0.2f %s", time, secsName];
    time -= hrs*secsPerHour;
    int mins = time/secsPerMin;
    time -= mins*secsPerMin;
    int secs = time;
    if(hrs == 0) return [NSString stringWithFormat:@"%d%s%02d %s", mins, timeSep, secs, minsName];
    return [NSString stringWithFormat:@"%d%s%02d%s%02d %s", hrs, timeSep, mins, timeSep, secs, hrsName];	

