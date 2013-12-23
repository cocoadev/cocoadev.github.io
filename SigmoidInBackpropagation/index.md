---
layout: page
title: SigmoidInBackpropagation
---

Can someone explain why sigmoid summing is necessary for backpropagation learning methods?

----

Someone on a neural network forum probably could do it better, but a quick reason is that without a continuous function, the learning algorithm fails (as it requires a derivative).  You can use any number of functions, but the sigmoid is generally used because it can simulate a 0/1 threshold function (e.g. f(x) = 1 iff x > t; otherwise 0), which is considered biologically plausible.

Next time, at least ask someone relevant to do your homework for you.

