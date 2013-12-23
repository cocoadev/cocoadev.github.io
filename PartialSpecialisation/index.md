---
layout: page
title: PartialSpecialisation
---



In CPlusPlus you can write a generic function or structure using templates. PartialSpecialisation refers to providing code for the generic function or structure (class) when one or more of the template parameters are more specialised.

For example, you might write some code that copies anything passed in.

    template <typename T> T replicate(T val)
    { return T(val); }

However, you want to copy the destination of a pointer if it's non-NULL, not the address it holds, so you can partially specialize your function to pointers:

    template <typename T> T* replicate(T* p)
    {
    if (p)
        return new T(*p);
    return NULL;
    }

Now     replicate will work more as intended. We could also partially specialize to replicate other containers, like vectors of pointers, in the same way:

    template <typename T> std::vector<T*> replicate(std::vector<T*> v)
    { ... }

This latter may not work on older compilers.

----

For example, you may write a generic memory copy function like this:

    
template <typename _InputIter, typename _OutputIter>
_OutputIter copy (_InputIter first, _InputIter last, _OutputIter out)
{
   for(; first != last; ++first)
      *out++ = *first;
}


This however is not the best way to copy byte sequences, so in addition we could provide this (full) specialisation:

    
template <>
_OutputIter copy<char*, char*> (char* first, char* last, char* out)
{
   memcpy(out, first, last-first);
}


This is mainly to write a more effective algorithm, but it can be used for something else. Let us say we write a function which will uppercase the first letter in each word. Our function should be generic in that we do not know the type of characters (could be unichar, char or wchar_t). We start by defining something called traits:

    
template <typename _CharT>
struct CharacterTraits
{
   static bool isLetter (const _CharT ch)       { return false; }
   static bool isUppercase (const _CharT ch)    { return false; }
}


And then we can specialise it for char and unichar:

    

template <>
struct CharacterTraits<char>
{
   static bool isLetter (const char ch)         { return isalpha(ch); }
   static bool isUppercase (const char ch)      { return isupper(ch); }
};

template <>
struct CharacterTraits<unichar>
{
   static bool isLetter (const unichar ch)
   {
      static NSCharacterSet *letter = [NSCharacterSet letterCharacterSet];
      return [letter characterIsMember:ch] == YES;
   }

   static bool isUppercase (const unichar ch)
   {
      static NSCharacterSet *uppercase = [NSCharacterSet uppercaseLetterCharacterSet];
      return [uppercase characterIsMember:ch] == YES;
   }
}


Well, we would also need one member function to actually convert the character to uppercase, but I think the idea is clear, we can now write code like this:

    
template <typename _CharT>
bool is_letter (_CharT ch)
{
   return CharacterTraits<_CharT>::isLetter(ch);
}

void MyFunction ()
{
   unichar ch1 = 'a';
   char ch2 = 'a';
   cout << is_letter(ch1) << ", " << is_letter(ch2) << endl;
}


This will use Cocoa to decide wether ch1 is a letter, but ANSI-C's isalpha() to decide if ch2 is a letter.

I realise that this turned out to be only about full specialisation. But that was probably the point I wanted to make anyway (since I brought it up only to show how to use traits).

