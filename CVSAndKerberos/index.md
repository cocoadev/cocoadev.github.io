---
layout: page
title: CVSAndKerberos
---

In Panther build 7b21 calling cvs calls kerberos authentication.  Anybody know a way around that?

It's not cvs per se, it's ssh. I don't think there's a fix; just the kind of bug that you have to deal with in seeds. You could switch to pserver access, which won't have the problem.

