---
layout: page
title: PreloadASound
---

I noticed that everytime I play a sound in one of my apps, there is a delay before the sound is played for the first time after the app is launched.
Is there a way to preload a sound so there isn't any loading delay ?

-- Trax

I assume you're using NSSound. I guess the answer-- useful or no-- would be to stop using NSSound and use QuickTime instead... but then you incur a ton of other penalties. NSSound is lazy... but easy.

-- RobRix

I've found that if the sound is not encoded at 44100 Hz, 16-bit stereo, then some behind-the-scenes conversion seems to be taking place. I converted all of my sounds in a recent app to 44.1 and the delays went away (for the most part).

-- Tantle

Personally, I'm a bit reluctant to use QT since I discovered that it can't play MP3s correctly... But you're probably right, RobRix. By the way, what are those "other penalties" of using QuickTime for sounds ?

-- Trax

Well, NSSound is probably using either QuickTime or CoreAudio, and QuickTime is probably using CoreAudio, but... The other penalties can be summed up as "complexity." NSSound is ripe for simple, one-line statements. QuickTime is *not*. You might also try one of the multitude of MP3 decoders available in source form if that's all you need... mpg123, I hear, works well.

-- RobRix

I heard about mpg123, but I still can't figure what to do with the 70 files contained in the package. And how to implement it in CoreAudio or QuickTime to get some results.

-- Trax

I can show you the door... but you're the one who has to walk through it (; Which is to say: you're already more experienced with it than I am. So play with it! And hopefully somebody more knowledgeable than I will be able to help also.

-- RobRix

