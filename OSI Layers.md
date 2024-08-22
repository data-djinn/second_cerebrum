#networking
## Application Layer
- applications create the data
## Presentation Layer
- Data is formatted & encrypted
## Session Layer
- Connections are established and managed
## Transport Layer
- data is broken into segments for reliable delivery
# Hardware (lower layers)
## Network Layer
- segments are packed into packets & routed
## Data Link Layer
- Packets are framed & sent to the next device
- node-to-node delivery of the message
- ensures the physical layer's data transfer is error-free
- whe
## Physical Layer
- lowest layer, contains information in the form of **bits**
- responsible for the physical transmission of bits from one node to the next
- Frames are converted into bits & transmitted physically
- Receives signal and converts it into bits (101011011000101110)
- after conversion, sends bits to data link layer, which reassembles the frame
### functions
##### Bit synchronization
- provides a clock that controls both sender & receiver
##### Bit rate control
- defines the transmission rate (bits per second)
##### Physical topologies
- Specifies how the different devices/nodes are arranged in a network (bus, star, mesh topology)
##### Transmission mode
- defines how the data flows between the 2 connected devices
	- simplex
	- half-duplex
	- full-duplex
### devices
- hub
- repeater
- modem
- cable
