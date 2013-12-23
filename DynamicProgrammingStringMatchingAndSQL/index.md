---
layout: page
title: DynamicProgrammingStringMatchingAndSQL
---

I'm in the process of writing a search tool and would like to use SQLite for persistant storage. I have already created a string matching function that uses simple "dynamic programming" ideas to create scores for partial matches. Does anyone know if SQLite has the ability to hook into my function to select records?

What would be ideal is if there was a way you could set a threshold for a SELECT. Say you would like to select all records with the first name Steve and set a threshold of 10 for the partial match deviation. 

I figured out a way to do it. I just have to modify     sqliteLikeCompare() and test the pattern for a special string of my picking (e.g. '%%%%THRESH='), and hook away!!

