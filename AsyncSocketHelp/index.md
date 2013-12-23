---
layout: page
title: AsyncSocketHelp
---



I'll try to describe the issue I'm having but I'd be more than happy to share my code with anyone willing to have a look and help me out.

I believe that I understand how AsyncSockets work, however I'm getting behavior that I'm not expecting.

My project is in it's infancy but I have a server app and a client app.  The client establishes a connection to a socket in the server and that socket is put into an array of sockets.  When my client sends the string "employeedata" the server should then forward, on the requesting socket, a string containing a list of employee information.

What happens is this:  client makes connection with server.  OK.  Client sends "employeedata" string request.  Server receives it and claims to be sending the data string back to the client.  OK.  The client, however, does not receive it.  When the client sends "employeedata" again, and any subsequent time, things work as I would expect.  Just that first time is the problem.

Now I know that people are going to say "post your code" but even thought the infant apps are relatively small I didn't know if it was acceptable to post so much code for a question here.

Thank you.

----

I am the author. You can e-mail me for more help, but first things first: After the client sends the request, does it call one of the read methods? Does the server terminate the employee data it sends properly?

----

Dustin,
Thanks.  I'm not sure how to contact you directly, though.

1)  yes, in my client's onSocket:didWriteDataWithTag:  I call readDataWithTag:

2)  I'm not sure what you mean by "terminate the employee data it sends properly"

Thanks

