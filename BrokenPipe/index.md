---
layout: page
title: BrokenPipe
---

I have an NSTask and an NSPipe which I use to send data to the task. I don't think there's anything wrong with my code, since I've used it before in other situations, but sometimes when it runs, it gives me this:

    
** Uncaught exception: <NSFileHandleOperationException> *** -[NSConcreteFileHandle writeData:]: Broken pipe


I know my task is still running when this happens (the task is sudo, so it can't quit until I've given it the password), so does anyone know what my problem might be?
Thanks.

- Eamon

