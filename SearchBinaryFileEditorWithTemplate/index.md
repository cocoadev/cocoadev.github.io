---
layout: page
title: SearchBinaryFileEditorWithTemplate
---

Hi,

I search for a binary file editor software with template concept.

I want to update a binary data like this :
    
VB001AABB<cr>
VB002CCDD<cr>
...
VB099YYZZ<cr>

search and replace substring at offset = 5, len = 2 with 'aa' so result is like that :

    
VB001aaBB<cr>
VB002aaDD<cr>
...
VB099aaZZ<cr>


Because I don't use a grapic software for this time, I use perl like this :*
perl -pi.orig -e "substr($_,5,2)='aa'"*

Do you known a software with a GUI who can do this (under mac os x of course...) ?

thanks for your reply.

