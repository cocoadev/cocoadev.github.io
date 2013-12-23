---
layout: page
title: CFUser
---

Hi, I want some information about CFUser notifications,because In my project I want to show alert message with "ok" & "cancel" buttons.

so can anyone idea how to create alert message through dialogue box and when click on either [ok or cancel] buttons to show the message when

u pressed corresponding button. I searched on the net and this forum i got one idea , that i implemented but iam getting problem when clicked on either buttons it showing the the same boolean value "No" .I used this function mentioned below.

CFoptionFlags? flags= kCFUserNotificationDefaultResponse | kCFUserNotificationAlternateResponse

Bool value = CFUserNotificationDisplayAlert?(0,kCFUserNotificationCautionAlertLevel , NULL,NULL,NULL, CFSTR("The given list size is"), CFSTR("CD size is not more than 700"),

CFSTR("OK"), CFSTR("Cancel"), NULL, &flags);

if(value) printf("u pressed ok button"); else printf(" u pressed cancel button");

is it possible to get information which button i pressed through this function or any other way to proceed....plz help if any one knows......if any thing is not understand , plz free to ask.... Thanku...

