---
layout: page
title: MultipleInstancesOfSameNSMovie
---



You know how in some applications you see a thumbnail movie view (with the movie playing) and then you can go to fullscreen which has that same movie already in progress.  How do you do this?  I basically have two views each with a QTMovieView and I want them both to reference the same movie so that when I switch views the movie doesn't reload in the new view.  This is partcularily needed because the movie content is a live stream and I don't want to load the stream twice (not enough bandwidth)...   any ideas?

EcumeDesJours

