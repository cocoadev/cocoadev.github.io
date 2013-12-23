---
layout: page
title: HashFunctions
---

here's some hash functions for those who like to optimize there core foundation dictionaries. These are C versions of the C++ versions post here -> http://www.partow.net/programming/hashfunctions/

When you supply the hash hook function for CFDictionaryHashCallBack you have to cast the function correctly (e.g.     keyCallbacks.hash = (CFHashCode (*)(const void *))ELFHash;)

C Hash Functions:
    
unsigned int RSHash(char *str) {
    unsigned int a = 63689, b = 378551, hash = 0, i, length = strlen(str);
    for(i = 0; i < length; i++) {hash = hash*a+str[i]; a = a*b;}
    return (hash & 0x7FFFFFFF);
}

unsigned int JSHash(char *str) {
    unsigned int hash = 1315423911, i, length = strlen(str);
    for(i = 0; i < length; i++) hash ^= ((hash << 5) + str[i] + (hash >> 2));
    return (hash & 0x7FFFFFFF);
}

unsigned int PJWHash(char *str) {
    unsigned int i, length = strlen(str);
    unsigned int BitsInUnignedInt = (unsigned int)(sizeof(unsigned int) * 8);
    unsigned int ThreeQuarters    = (unsigned int)((BitsInUnignedInt  * 3) / 4);
    unsigned int OneEighth        = (unsigned int)(BitsInUnignedInt / 8);
    unsigned int HighBits         = (unsigned int)(0xFFFFFFFF) << (BitsInUnignedInt - OneEighth);
    unsigned int hash             = 0;
    unsigned int test             = 0;

    for(i = 0; i < length; i++) {
        hash = (hash << OneEighth) + str[i];
        if((test = hash & HighBits)  != 0) hash = (( hash ^ (test >> ThreeQuarters)) & (~HighBits));
    }
    return (hash & 0x7FFFFFFF);
}

unsigned int ELFHash(char *str) {
    unsigned int hash = 0, x = 0, i, length = strlen(str);
    for(i = 0; i < length; i++) {
        hash = (hash << 4) + str[i];
        if((x = hash & 0xF0000000L) != 0) {hash ^= (x >> 24); hash &= ~x;}
    }
    return (hash & 0x7FFFFFFF);
}

unsigned int BKDRHash(char *str) {
    unsigned int seed = 131, hash = 0, i, length = strlen(str);
    for(i = 0; i < length; i++) hash = (hash*seed)+str[i];
    return (hash & 0x7FFFFFFF);
}

unsigned int SDBMHash(char *str) {
    unsigned int hash = 0, i, length = strlen(str);
    for(i = 0; i < length; i++) hash = str[i] + (hash << 6) + (hash << 16) - hash;
    return (hash & 0x7FFFFFFF);
}

unsigned int DJBHash(char *str) {
    unsigned int hash = 5381, i, length = strlen(str);
    for(i = 0; i < length; i++) hash = ((hash << 5) + hash) + str[i];
    return (hash & 0x7FFFFFFF);
}


--zootbobbalu

----

Very nice indeed, but how and why do I use them?

