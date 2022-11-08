[[networking]]
<<<<<<< HEAD

- client-server architecture

# HTTP/1.1

- every computer gets an IP address
#### TCP
- you can establish an outbound TCP connections to another peer (which is what a client usually does) or listen for and accept incoming TCP connections (which is what a server usually does)
- once that's done, you get a bidirectionaly socket, from which you can read bytes from & write bytes to
	- in-order, reliable delivery (checksums & retransmission)
- since there are a lot of peers & thus a lot of packets, there are protections for the network (congestion) & server (flow control)
- TCP is basically writing text to some server & getting text back
