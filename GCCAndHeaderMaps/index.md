---
layout: page
title: GCCAndHeaderMaps
---

Does anybody know what the headermap is all about?

It seems to be a cache of headers or similar consulted by GCC (Xcode provide special undocumented preprocessor instructions to point at these headers).

The reason I am asking is, that I am trying to create a makefile for my project, and it seems a tad slower than when building from Xcode (my makefile does use precompiled headers).

Additionally, my makefile generates the dependencies with the -MM option, which I would assume Xcode also use, but this does not show up in the detailed build report, and it is actually quite slow (generating dependencies for some files take longer than actually compile them), so perhaps the header map will also improve the performance for this dramatically?

----

README.Apple from the GCC distribution says:
    
Header mapfiles

This is the support for a PB feature where actual pathnames for
headers come from a given file rather than being searched for in the
various include paths.  It's not useful outside of the PB environment.


Looking in the source of cpp, it seems that gcc can only read these files, so PB (Xcode) would probably be responsible for writing them. It is a binary format which can be seen in cpplib.h:
    
struct hmap_bucket
{
  uint32 key;          /* Offset (into strings) of key                */
  struct {
    uint32 prefix;     /* Offset (into strings) of value prefix   */
    uint32 suffix;     /* Offset (into strings) of value suffix   */
  } value;             /* Value (prefix- and suffix-strings)          */
};

struct hmap_header_map
{
  uint32 magic;             /* Magic word, also indicates byte order       */
  uint16 _reserved;         /* Reserved for future use -- zero for now     */
  uint32 strings_offset;    /* Offset to start of string pool              */
  uint32 count;             /* Number of entries in the string table       */
  uint32 capacity;          /* Number of buckets (always a power of 2)     */
  uint32 max_value_length;  /* Length of longest result path (excl. '\0')  */
  struct hmap_bucket buckets[0]; /* Inline array of 'capacity' maptable buckets */
  char strings[0];          /* String contents follow the last bucket      */
};


There is actually a tool (pbxhmapdump) in /Developer/Tools/ to dump the contents of a header file.

I do not know if the map serves as a cache (to compensate for a slow filesystem) or if it is only so that PB won't have to add all the include directories for project headers to the GCC command line options � although this will cause problems for projects which have identically named headers (placed in different directories).

