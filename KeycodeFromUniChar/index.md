---
layout: page
title: KeycodeFromUniChar
---

Hi All, 

The program I'm trying to create is to increase accessibility to text fields so for a given set of preset strings the user can click a button and the app will post that string (or single key) to whichever app is active. I found the method CGPostKeyboardEvent but it requires a keycode as opposed to a string. So in theory I thought I would iterate through each character in my string and "post" them separately. I'm trying to figure out how to get the keycode for a given character. 

Thanks in advance for any help.




----

This is not exactly what you asked for (nor it is a perfect solution), but it's close enough that I'm sure it will be of some help to you.  I got most of this code elsewhere, from a google cache of an online forum post I can't find anymore (that was written in french, and loosely translated by me). I wish I could take credit, but somebody smarter than me figured it out.

    

BOOL Ascii2Virtual(char pcar, BOOL *pshift, BOOL *palt, char *pkeycode) 
{
    KeyboardLayoutRef keyboard;
    const void *keyboardData; // keyboard layout data
    UInt16 nbblocs;
    char *modblocs, *blocs, *deadkeys;
    int ix, ifin, numbloc, keycode;
    
    BOOL shift, alt;
    
    // r�cup�ration du clavier courant
    // get the current keyboard
    if(KLGetCurrentKeyboardLayout(&keyboard)) return NO;
    
    // r�cup�ration de la description (keyboard layout) du clavier courant
    // get the description of the current keyboard layout
    if(KLGetKeyboardLayoutProperty(keyboard, kKLKCHRData, &keyboardData)) return NO;
    
    // r�cup�ration du pointeur de d�but des num�ros de blocs pour chaque combinaison de modifiers
    // get pointer early numbers of blocks for each combination of modifiers
    modblocs = ((char *)keyboardData) + 2;
    
    // r�cup�ration de nombre de blocs keycode->ascii
    // get number of blocks keycode->ascii
    nbblocs = *((UInt16 *)(keyboardData + 258));
    
    // r�cup�ration du pointeur de d�but des blocs keycode->ascii
    // get pointer early blocks keycode-> ascii
    blocs = ((char *)keyboardData) + 260;
    
    // on d�termine la taille de toutes les tables keycode->ascii � scanner
    // determining the size of all tables keycode-> ascii a scanner
    ifin = nbblocs*128;
    
    // on d�termine le pointeur de d�but de la tables des dead keys
    // determining pointer early in the tables of dead keys
    deadkeys = blocs+ifin;
    
    // maintenant on parcourt les blocs keycode->ascii pour retrouver le car ascii
    // Now it runs blocks keycode-> ascii to find the car ascii
    for (ix=0; ix<ifin; ix++)
    {
        if (blocs[ix]==pcar)
        {
            // car ascii trouv� : il faut d�terminer dans quel bloc (num�ro du bloc) il se trouve
            // found ascii value: now we must determine which block it is
            keycode = ix & 0x7f; // 0111 1111 mask
            numbloc = ix >> 7;
            break;
        }
    }
    
    // car non trouv� : on termine (avec erreur)
    // not found: bail out (error)
    if (ix >= ifin) return NO;
    
    // � partir du num�ro de bloc, il faut retrouver la combinaison de modifiers utilisant ce bloc
    // from block number, we must find the combination of modifiers using this block
    for (ix=0; ix<15; ix++)
    {
        // on ne traite pas si les modifiers ne sont pas "majuscule" et "option"
        // it does not address whether the modifiers are not "capital" and "option"
        if (ix&1 || ix&4) continue;
        
        // combinaison de modifiers trouv�e pour le bloc
        // Combining modifiers found for the block
        if (modblocs[ix]==numbloc)
        {
            shift = (ix&2) ? YES : NO;
            alt   = (ix&8) ? YES : NO;
            break;         
        }
    }
    
    // combinaison modifiers non trouv� : on termine (avec erreur)
    // combination modifiers not found: bail
    if (ix>=15) return NO;
    
    // mise � jour des param�tres
    // save our parameters
    *pkeycode=keycode;
    *pshift=shift;
    *palt=alt;
    
    return YES;
}



Don't ask me how the guy who wrote that figured it out, but I can assure you that it does indeed work :P  You could then write a simple method to get every character in a string and send them.

    

- (int)keyCodeForCharacter: (NSString*)character {
    if(![character length]) return -1;
    
    char code;
    BOOL shift, alt;
    if(Ascii2Virtual( (char)[character characterAtIndex: 0], &shift, &alt, &code)) {
        return code;
    }
    return -1;
}



I hope this helps you. - Jon

