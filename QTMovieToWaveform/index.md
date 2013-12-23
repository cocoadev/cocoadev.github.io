---
layout: page
title: QTMovieToWaveform
---

Any thoughts on how to generate a standard amplitude waveform I could draw from a QTMovie?

Use the MovieAudioExtraction API <http://developer.apple.com/quicktime/audioextraction.html > to get successive buffers of 32-bit floating point PCM data corresponding to the summary mix of your movie's audio tracks.

The data will be scaled between -1.0 and 1.0. Whether stereo/multi-channel samples will be interleaved or stored in separate buffers is determined by how you fill out the fields of an AudioChannelLayout structure.

Once you have the samples in an acceptable format, just scale amplitudes according to your window (or image) dimensions and plot your graph using OpenGL, CoreGraphics, or your preferred graphics API.

----
Wow!  Thank you!  PCM data is EXACTLY what I wanted.

