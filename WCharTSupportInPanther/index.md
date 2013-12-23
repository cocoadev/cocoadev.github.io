---
layout: page
title: WCharTSupportInPanther
---

Apple said that they would add wchar_t support in Panther and according to this page: http://developer.apple.com/documentation/General/DeveloperTools/Conceptual/General/MovingProjectsToXcode/migration_differences/chapter_2_section_11.html they say:

*The standard C and C++ wide character type wchar_t and the wstring template class that makes use of it are supported in the system libraries for Mac OS X version 10.3 [...] if you need to use these data types in software that will run in versions of Mac OS X prior to Panther, you should obtain a third-party standard C library*

Now I am asking myself � what did they actually add? IIRC wstring and friends worked fine under Jaguar, although toupper would fail for non-ASCII characters, but this situation is exactly the same under Panther.

I.e. this will give compile error:     cout << L"�blegr�d" << endl; and so will this     cout << wstring(L"�blegr�d") << endl;. And writing code like this:
    
wchar_t upcase (wchar_t const& ch) { return std::toupper(ch, std::locale("da")); }
...
wchar_t const* str = L"�blegr�d";
transform(str, str + char_traits<wchar_t>::length(str), 
   ostream_iterator<char>(cout), upcase);

Will output     �BLEGR�D, i.e. not     �BLEGR�D.
