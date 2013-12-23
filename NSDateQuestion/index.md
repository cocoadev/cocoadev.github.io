---
layout: page
title: NSDateQuestion
---

Hi,

Why this code doesn't works fine ?! tmpDate != dateNew

    

  {
    NSDate  *tmpDate = [NSDate date];
    
    NSNumber *tmpNum = [NSNumber numberWithFloat:[tmpDate timeIntervalSince1970]];    
    NSDate *dateNew = [NSDate dateWithTimeIntervalSince1970:[tmpNum floatValue]];
    
    NSLog(@"%@", [tmpDate descriptionWithLocale:nil]);
    NSLog(@"%@", [dateNew descriptionWithLocale:nil]);
  }



    

[Switching to process 4649 local thread 0x2e03]
Running�
Current language:  auto; currently objective-c
2010-08-14 14:03:25.280 NSCalendar2[4649:813] 2010-08-14 14:03:24 +0200
2010-08-14 14:03:25.731 NSCalendar2[4649:813] 2010-08-14 14:03:12 +0200



----

You must use double instead of float

    
  {
    NSDate  *tmpDate = [NSDate date];
    
    NSNumber *tmpDouble = [NSNumber numberWithDouble:[tmpDate timeIntervalSince1970]];
    NSDate *dateNew = [NSDate dateWithTimeIntervalSince1970:[tmpDouble doubleValue]];
    
    NSLog(@"%@", [tmpDate descriptionWithLocale:nil]);
    NSLog(@"%@", [dateNew descriptionWithLocale:nil]);
  }


    
2010-08-14 16:03:28.998 NSCalendar2[4748:813] 2010-08-14 16:03:01 +0200
2010-08-14 16:03:29.448 NSCalendar2[4748:813] 2010-08-14 16:03:01 +0200


JM Marino
http://jm.marino.free.fr

