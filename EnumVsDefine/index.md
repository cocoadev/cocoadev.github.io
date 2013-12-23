---
layout: page
title: EnumVsDefine
---

When watching an old WWDC session, I remember the speaker talking about using     typedef enum and     #define to label integer constants. She stated that     typedef enum was better than using macros. Does anyone know why this is?

----

From what I've learned... macros = more physical memory but variables = more system memory. Macros are just search-and-replace by the compiler. Variables are actually compiled and used once, while a macro's code is used wherever the macro is called. *..though the amount of memory we're talking about is totally insignificant.  Plus constant strings and integers don't take up any more space when used more than once.*

----

One big reason to use enums is that many debuggers (including current versions of gdb, I believe) don't understand #defines, but they understand enums. So using     someEnum in an expression in gdb will work, but using     SOME_DEFINE in an expression will fail.

----

I also believe that     typedef enum allows for better compile-time type checking than     #define.

----

Are enums treated as global variables (i.e. do they take up space in the symbol table)?

----

Indeed the advantage of enums is that they allow for compile-time type checking. In worst-case they generate exactly the same code as had you used a macro (they are NOT in any symbol tables). In best-case the code might be better, because you can use the enum type instead of e.g. an integer, and the former can have smaller storage requirements. Although the compile time type-check (and better readability in your code) are the reasons to favor enums.

Personally I embed enums in a namespace like this:
    
namespace alignment {
   enum type { left, right, center, justify };
};

This allows code like this:
    
void set_alignment (alignment::type);
...
set_alignment(alignment::center);


----

Thanks for the info!! I'm glad enums are not included in any symbol tables. I have been using the advice from the WWDC session blindly and have loaded many of my headers with a bunch of enums. I was in the process of refactoring some code to make the object files smaller, so I could no long stand using the WWDC advice without knowing WHY.

----

    #defines are also terribly evil the moment you start using C++, because they don't respect namespaces (like classes). The following will break horribly:

    namespace first {
#define left 0
#define right 1
#define center 2
}

namespace second {
  class myclass
    {
  public:
    void center() { }
    };
}

int main()
  {
  second::myclass temp;
  temp->center();
  return 0;
  }


Gnu gives the following errors:

    11: warning: declaration does not declare anything
11: error: parse error before numeric constant
11: error: missing ';' before right brace
In function `int main()':
18: error: parse error before numeric constant


This is because     center has been universally redefined to     0, and so cannot be used as a method name! Ouch.

*This is one of the primary reasons that     #defines are named using     ALL_UPPER_CASE. This effectively bumps them into a separate namespace from everything else. Of course, it won't prevent your     #defines from conflicting with other people's, but it will at least prevent them from conflicting from other types of things. Being able to use lowercase names and get decent error messages when things conflict is another argument for using enums when possible.*

