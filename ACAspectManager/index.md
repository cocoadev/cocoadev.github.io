---
layout: page
title: ACAspectManager
---



    


// AdviceProtection
+(void)enableAllAdvice;
+(void)disableAllAdvice;
+(void)protectAdvice;
+(void)unprotectAdvice;

/*
    Controls Debug logging
    Log nothing
*/
+(void)quiet;

/*
    Controls Debug logging
    Log everything
*/
+(void)verbose;

/*
    Controls Debug logging
    Log information relevant to code generation
*/
+(void)genOnly;

