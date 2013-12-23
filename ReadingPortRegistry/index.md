---
layout: page
title: ReadingPortRegistry
---

When you vend an object and register the port's name it gets registered system wide so you can pass in the port's name and get a connection in another process etc.  There must be something somewhere that tracks the vended ports and what their names are.  Does somebody know a way to get a list of the names of the named ports?

it appears you can get them with the function mach_port_names in the header /usr/include/mach/mach_port.h...  I am not sure about all the arguments you need to pass or what the return type is.

kern_return_t mach_port_names ( ipc_space_t task, mach_port_name_array_t* names, mach_msg_type_number_t* namesCnt, mach_port_type_arry_t* types, mach_msg_type_number_t* typesCnt );

I think if you just declare variables of each type and pass them in with empty values it should return the values in the variable you passed... I think...  (although querying what you get back is another thing....)

