[[Filecoin]] [[web3]] [[]]

# Concepts
==IPFS is a distributed system for storing and accessing files, websites, applications, and data==

- rather than pinging a centralized server (like Wikipedia), you can use an ipfs content address
	- `/ipfs/bafybeiaysi4s6lnjev27ln5icwm6tueaw2vdykrtjkwiphwekaywqhcjze/wiki/Aardvark
	- add `https://ipfs.io` to the start to fetch the web from the ipfs network
- ipfs client doesn't only download - it also uploads to the network
- ipfs makes this possible for not only web pages, but **any kind of file a computer might store**
	- documents
	- emails
	- database records
### Decentralization
- making it possible to download a file from many locations that aren't managed by one location (like s3)
	- **supports a resilient internet**: if wikipedia's servers go down, you can still access wikipedia's knowledge
	- **makes it harder to censor content**
	- **can speed up the web when you're far away or disconnected**: like a open-source CDN
### Content addressing
- the hash in the ipfs address is called a *content identifier*
	- this is how the content can be seeded from multiple flaces
- Instead of being domain-based, IPFS addresses a file by *what's in it*