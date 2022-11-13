# Features
## Pooled storage
- combines the features of a file system and a volume manager
- unlike other file systems, ZFS can create a file system that spans across a series of drives or a pool
	- add storage to a pool by adding another drive - ZFS handles partitioning & formatting
![[Pasted image 20221113124344.png]]
## Copy-on-write
- on other filesystems, when data is overwritten, it is lost forever
- ZFS writes the new information to a different block
	- once the write is complete, the filesystem's metadata is updated to point to the new info
	- if the system crashes while the write is taking place, the old data is preserved
	- the system does not need to run `fsck` after a system crash
## Snapshots
- track changes in the file system
- snapshot contains the original version of the file system, and the live filesystem contains any changes made since the snapshot was taken
- no additional space is used
- as new data is written to the live file system, new blocks are allocated to store this data
- if a file is deleted, the snapshot reference is removed as well
	- so, snapshots are mainly designed to track changes to files, but not the addition and creation of files
- snapshots can be mounted as read-only to recover a past version of a file
	- it's also possible to rollback the live system to a previous snapshot
		- all changes made since the snapshot will be lost
## Data integrity verification & automatic repair
- whenever data is written to ZFS, it creates a checksum for that data
- when the data is read, the checksum is verified
- if the checksum does not match, then ZFS knows that an error has been detected
## RAID-Z
- ZFS can handle RAID without requiring any extra software or hardware
## Maximum 16 Exabyte file size
## Maximum 256 Quadrillion Zettabytes storage
- designed to be the **last word in file systems**
- 16 billion **billion** times the capacity of 64-bit systems
- fully populating a 128-bit storage pool would, literally, require more energy than boiling the oceans