## Early days
- in the 1940s, computers were massive, single-threaded, & disconnected from one another
	- programmed by flipping switches & plugging wires
	- data input/output via punch cards
- in the 1970s, time-sharing became common
	- many humans could access the same computer
	- tasks from all users would be queued & executed sequentially
	- later, each task could be split into subtasks & multitasked
	- data input/output via terminals (even remotely!)
- ARPANET is created, initially to perform "data interchange" so multiple computers could collaborate in solving a hard problem
	- eventually, a standard protocol for remotely controlling computers emerged, caled Telnet
	- Telnet initially ran over ARPANET's "NCP" protocol, it transitioned to **TCP/IP in 1983**
		- tcp/ip is the start of the modern internet

### How to connect computers?
- since computers are electronic machines, the simplest way to connect them is a cable (direct connections)
	- with more than 2 computers, we need a *network interface controller* (NIC)
![[Pasted image 20221107191032.png]]
	- things get more complicated the more computers join the network
	- so instead of a messy spiderweb, we make a 'bus' instead
		- essentially a long cable that all other cables plug into:
![[Pasted image 20221107191212.png]]
==this is the physical layer==
New problems now emerge:
- everybody is now talking at the same time
- nobody can tell who anybody else is
- nobody ever shuts up

- so, a sort of "time share" is set up that divides a unique resource  (bus) into slices (datagrams) so that users have the *illusion* of multiple conversations happening at once
	- in reality, only a single conversation happening at once - we just switch very often between all open conversations
![[Pasted image 20221107191509.png]]

- to know who is who, we give every NIC a unique addresses
	- if we give the address 6 bytes, that should be enouh for 281 trillion unique addresses
	- these addresses are called MAC ("media access control"), because they help controlling acess to a media (the bus)
	- and now we will include our MAC address in every datagram anyone sends - both the *source* MAC and the *destination* MAC
		- so now computers know the sender, and if the datagram is meant for them
	![[Pasted image 20221107191812.png]]
- since all computers are on the same bus, they can hear everything
	- so as long as they hear chatter, they shut up
	- in order to not all start up as soon as there is silence, each conversation waits a random amount of time
	- if all else fails, we add a checksum to all datagrams, so we know if something bad happens & we discard the datagram
	- leave it to the above protocol to retransmit the failed datagram
==this is the data layer - Ethernet!==

More problems:
- if we send data over long distances, the delay interferes with our collision detection strategy
	- over half a megabyte could be in transit before a bit sent from Madrid is recieved in London
	- how to implement the above protocol at great distance to enable parallel conversations?
	- this is solved by making "hubs" that have many, many ethernet ports
		- whenever they recieve something on a port, they echo it verbatim to all other ports
- now that we have hubs, it would be useful to separate datagrams into intra-country datagrams & inter-country datagrams
	- whereas MAC addresses were uniquely assigned to each network interface & had prefixes by manufacturer, the addresses we need now should be prefixed by *region* (or, at least, "group of nodes" that are related by their interconnection)
		- every computer connected to the "paris" hub should have an address like `109.208.x.x` (where every x is an integer between `0` & `255`)
		- Madrid can have `176.84.x.x`
	- this way, if we specify the source address and destination address in every datagram we send, the central "hubs" in Madrid & Paris would know if they were meant to be sent in-country, or outside the country
	- they thus know where to *route* them... let's rename our hubs to **routers**
![[Pasted image 20221107193546.png]]
![[Pasted image 20221107193638.png]]
==This is IPv4==

- we can now connect everyone in the world, but remembering everyone's IP addross is annoying
- so let's figure out a system to attribute **human-readable names** to *some* of the nodes in the network
	- ==this is DNS==, Domain Name Service
![[Pasted image 20221107193912.png]]

- the IP address of each computer shouldn't be static, but *dynamically assigned* by the router
	- so that the router makes sure every computer on its network has IP addresses in the same range, and no two computers ever have the same IP address
	- ==this is DHCP==, Dynamic Host Configuration Protocol
![[Pasted image 20221107194105.png]]

- the regular computer doesn't know who to send the DHCP request to, nor what its own address is
	- so we have to reserve some special IP addresses for **broadcasting**, i.e. sending packets to *everyone on the network* at first
	- this address is `255.255.255.255`
- we can have a lot of other protocols:
	- reconfigure routes between countries
	- exchange structured information/files (FTP/SFTP)
	- some protocols would be reliable, and others would be lossy
	- know if a given IP address is *reachable* from our current connection ==ping==