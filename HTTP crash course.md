[[networking]]

# HTTP/1.1

- every computer gets an IP address
#### TCP
- you can establish an outbound TCP connections to another peer (which is what a client usually does) or listen for and accept incoming TCP connections (which is what a server usually does)
- once that's done, you get a bidirectionaly socket, from which you can read bytes from & write bytes to
	- in-order, reliable delivery (checksums & retransmission)
- since there are a lot of peers & thus a lot of packets, there are protections for the network (congestion) & server (flow control)
- TCP is basically writing text to some server & getting text back
	- `netcat` library can open TCP connections on the port specified & send HTTP/1.1 headers
`printf 'HEAD / HTTP/1.1\r\nHost: neverssl.com\r\n\Connection: close\r\n\r\n | nc example.org' 80`
		- there are 65536 ports to choose from! (u16)
		- `netcat` handles DNS lookup for us! (turning example.org into an IP address)
		- if we need to do the DNS lookup ourselves, we can use the `dig` package
`dig +short A example.org` for IPv4, `dig +short AAAA example.org`

- HTTP headers are made up of the "method":
	- `GET`: gimme that 
	- `POST`: submit form/upload, `HEAD`: like `GET` but only headers (no response body)
	- `DELETE`
	- `OPTIONS` for cross-origin resource sharing (CORS)
	- etc.
- then the path (`/`)
- and the HTTP protocol version, which is **always `HTTP/1.1` and nothing else**

- the path determines what resource we want to work on:
	- if we specify a resource that doesn't exist, we will get a `404` response
- we can send a body ("payload") with our request
	-  a payload can by any arbitrary bytes
	- put it after the empty line (`\r\n\r\n`)
	- you need to specify how long it is ("`Content-Length: 27`")
	- this allows the server to know the difference between "a client sent the whole request body then went away" & "a client cut its connection to us mid-request body"
- the server only closes the connection because we asked it to, with a `Connection: close` request header
- if we don't, it keeps the connection open, ready for another request
- if you are streaming a body, you don't know the `Content-Length`
	- send in chunks
	- every chunk is prefixed by `L\r\n`, where `L` is the length of the next chunk in hexadecimal (e.g. `I am chunked` is 12 bytes long, hence `0xC`)
	- signal you're done with a chunk of length 0
	- again, the server can then know if you're actually done or if the client went away mid-transmission
#### In summary
- HTTP/1.1 is a text-based, human readable format
- request & response header are separated by CRLF (`\r\n`), and contain various bits of metadata about a request
- requests are made over TCP connections
	- multiple requests can be made over the same connection, one after the other
- after the header, the body begins (if there is one)
	- body can be written all at once, or in chunks (prefixed with their hexadecimal length)
#### Proxying HTTP/1.1
- if we are a CDN (Content Delivery Network) or an ADN (Application Delivery Network): we operate "edge nodes" at various locations around the world and proxy the requests back to some "worker nodes"
	- first, we need to accept TCP connections
	- as the first line of defense, this already raises questions: do we rate limit? how?
	- limiting the overall number of connections to service is fairly easy, but it gets harder if you want to limit "per IP address" or "per AS"
- for each connection we accept on port 80, we must be ready to read HTTP/1.1 requests - that means reading the request header
	- relaying the request to the requested resource is the most obvious action, but we need to verify that the client is not malicious
		- a well-behaved client will send a few more headers and then an empty line to indicate the end of the header
		- a malicious client could send a header of infinit length
			- most servers protect against that with a `431` error (`431` if the URI is too long, or `400` as a general catchall)
		- they can also send reasonably-sized headers, but lots of them
		- they can simply open a TCP connection & just sit there, doing nothing... opening many of these connections will eventually break your service
- After you protect against those attacks, you could have a maliscious client send a reasonable amount of HTTP requests headers, of reasonable size, but it sends them... one... byte... at.. a... time... slowly... --- this is a slowloris attack
	- these attacks can be very effective if they can find an endpoint that accepts POST requests
- so for all these reasons, we want to wait until we've recieved the *full* HTTP request header before establishing a connection to the "backend" or "app" or "upstream"
- there's also the trade off between fidelity & safety - lots of request misconfigurations (innocent & otherwise) can occur
- HTTP/1.1 proxies need to be *opinionated*, because there is a lot of ways in which the specifications can be interpretted & a variety of attacks that can be performed against HTTP endpoints

