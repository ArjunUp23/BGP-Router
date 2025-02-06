BGP Router:

Project 3 for CS 4700 - Network Fundamentals at Northeastern University

This program implements a BGP router to communicate with other routers in a simulation of a peered network topology.
The client program communicates using select() to tell which router is talking to us, and then processing that message.

This Program was made with the program specs detailed on 4700.network 
This Program was tested with the config tests made for this project.

Runs with the command:

$ ./4700router <asn> <port-ip.add.re.ss-[peer,prov,cust]> [port-ip.add.re.ss-[peer,prov,cust] ...]

And supports the following messages: 

- Handshake Messages
- Route Update Messages
- Route Withdraw Messages
- Data Messages
- Dump and Table Messages


Table of Contents:

* High-level overview of the program
* Challenges faced 
* Overview of how this program was tested


- High-level overview of the program:

This program, at a High-level, works in the following steps:

First, initializes program by setting the global variables, command line parsing, sending handshakes, etc.
Next, after all of the initialization is done, the program is constantly looping and using select to 
identify when a neighbor is trying to communicate
Then, the message is processed based on its type:

Route Update Messages:
	+ saves a copy of the announcement for when withdraws are received
	+ adds the updates to our forwarding table
	+ potentially send copies of the announcement to neighboring routers if the relationships are right.
	+ then aggregates the table based on the given updates and if any simplification can be done

Route Withdraw Messages:
	+ saves a copy of the announcement for when we have to rebuild the table
	+ removes the withdrawn routes from our forwarding table
	+ potentially send copies of the announcement to neighboring routers if the relationships are right.
	+ then rebuilds the table based on the given updates and if any simplification/disaggregation must be done

Data Messages:
	+ First sees which route (if any) in the forwarding table is the best route to use for the given destination IP
	+ If no route found, send back to the source with an error message
	+ If a route is found, but sending it does not benefit us, the data packet is not forwarded and is sent back.

Dump and Table Messages:
	+ When our router is sent a 'dump' message it responds with a copy of our forwarding table
	+ this is used by the project tests to ensure our routing table is correct

- Challenges faced:
The main challenge faced for me was the aggregation. Figuring out how to aggregate the table without messing up the routes took
the longest time out of any aspect of this project. What helped me overcome this challenge was actually using a whiteboard
and writing out the aggregation steps and helpers needed in pseudocode before coming back to actually typing it out.

Another challenge that I faced was using global dictionaries and variables in python. I am pretty new to the language and even
though it is very easy to learn, this was my first time using classes and global dictionaries in python. This challenge
was overcome by looking at A LOT of man pages and figuring out how stuff I have done in other languages can translate into
python.

The last challenge I will discuss was cleaning up my program. With large-scale code like this often times when I finish,
the end result comes out sloppy even if it works. With this project after I finished I spent a long time defining helpers and
adding comments (if anything, too many) to ensure the readability was there.

- Overview of how this program was tested:
This program was tested mainly through the config files given by the course staff. I followed the implementation guidelines
in the assignment, and as such went in order of the tests. After each level I used the configs to ensure the correct
functionality.

If the test was failing I used a combination of the text printed by the ./run program as well as my own print statements
I put within my program to debug the issues found. Because of the easy-to-read JSON format, this method of debugging was
more than enough to get my code working well.
