[[Nix]] [[ZFS]]
- erase your systems at every boot
- over time, a system collects state on its root partition
	- this state lives in assorted directories like `/etc` & `/var`
	- represents every under-documented/out-of-order step in bringing the services online
- immutable infrastructure elimiinates many of these forgotten steps
	- deleting & replacing your servers on a weekly or monthly basis means you are constantly testing & exercising your automation & deployment runbooks
- the key is **indiscriminate removal of system state**
- this works great when your servers:
	- can be provisioned / destroyed with an API call
	- aren't inherently stateful (e.g. a database)
#### Long running servers cause long outages
- servers in which immutaible infrastructure *doesn't work* are the servers that need good tools the most
- they accrete tweaks & turn into an ossified snowflake with brittle, load-bearing arms
#### FHS isn't enough
- it's hard to know exactly where your application state ends & where the OS, software, & config begins
- legacy OSs and the Filesystem Hierarchy Standard (FHS) poorly seperate these distinct areas of concern
	- e.g. `/var/lib` is for state information, `/etc` is not often configured on purpose
- replicating production, testing changes, undoing mistakes are all made harder by this accumulation of junk

## Nixos
- NixOS can boot with only 2 directoriies: `/boot` & `/nix`
	- `/nix` contains read-only system configurations, which are specified by your `configuration.nix` & tracked as immutating system generations
	- once the files are created in `/nix`, the only way to change the config's contents is to build a new system configuration with the contents you want
		- the only way to change the config's contents is to build a new system configuration & rebuild
	- **any configuration/files created on the drive outside on /nix is state & cruft**
		- ideally, you can lose everything outside of `/nix` & `/boot` & have a healthy system
		- you want to explicitly opt in & *choose* which state is important, & keep only that
#### Nixos boot sequence
- same basic steps as a standard Linux distro:
	- kernel starts with initial ramdisk
	- initial ramdisk mounts the systemdisks
- NixOS configures the bootloader to pass in the system configuration
	- that's how rollbacks are performed
	- also the key to erasing our disk on each boot
	- that parameter is named `systemConfig`
- On every startup, the boot stage knows what the system's configuration should be (stored read-only in the `/nix/store`)
	- early boot then manipulates `/etc` and `/run` to match the chosen setup - i.e. swapping out symlinks
	- **if /etc doesn't exist, early boot *creates* `/etc` & moves on as if it were any other boot**
		- it also **creates `/var`, `/dev`, `/home`, and any other core directories that must be present**

##### Opting out of saving data *by default*
- set up your filesystem in a way that lets you easily & safely erase the unwanted data, while preserving the data you do want to keep
	- use a ZFS dataset & roll it back to a blank snapshot before it is mounted
	- if you have a lot of RAM, you could skip the erase step & make `/` a tmpfs
- partition your disk with 2 partitions:
1. boot partition
2. ZFS pool
	- then create & mount a few datasets:
		- Root dataset: `zfs create -p -o mountpoint=legacy rpool/local/root`
		- create a snapshot before mounting, while it is totally blank: `zfs snapshot rpool/local/root@blank`
	- then mount it: `mount -t zfs rpool/local/root /mnt`
	- then mount the partition you created for the `/boot`: `mkdir /mnt/boot && mount /dev/the-boot-partition /mnt/boot`
	- create & mount a dataset for `/nix`:
```shell
zfs create -p -o mountpoint=legacy rpool/local/nix
mkdir /mnt/nix
mount -t zfs rpool/local/nix /mnt/nix
```
& a dataset for `/home`:
```shell
zfs create -p -o mountpoint=legacy rpool/safe/home
mkdir /mnt/home
mount -t zfs rpool/safe/home /mnt/home
```
- finally, **a dataset explicitly for state that you want to persist between boots:**
```shell
zfs create -p -o mountpoint=legacy rpool/safe/persist
mkdir /mnt/persist
mount -t zfs rpool/safe/persist /mnt/persist
```
- back up datasets under `rpool/safe`, never back up `rpool/local`

after devices are availabl, roll back to the blank snapshot:
```nix
{
	boot.initrd.postDeviceCommands = lib.mkafter ''
		zfs rollback -r rpool/local/root@blank
	'';
}
```
- if all goes well, your nex boot will start with an empty root partition but otherwise be configured exactly as you specified

### Opting in
- choose differently based on the role of the system (laptop/server)
Examples:
- Wireguard private keys: `mkdir -p /persist/etc/wireguard/`
```nix
{
	networking.wireguard.interfaces.wg0 = {
		generatePrivateKeyFile = true;
		privateKeyFile = "/persist/etc/wireguard/wg0";
	}
}
```
- NetworkManager connections (for laptops): `mkdir -p /persist/etc/NetworkManager/system-connections`
```nix
{
	etc."NetworkManager/system-connections" = {
		source = "/persist/etc/NetworkManager/system-connections/"
	};
}
```
- bluetooth devices (laptops): `mkdir -p /persist/var/lib/bluetooth`
```nix
{
	systemd.tmpfiles.rules = [ 
		"L /var/lib/bluetooth - - - - /persist/var/lib/bluetooth"
	];
}
```
- ssh host keys: `mkdir -p /persist/etc/ssh`
```nix
{
	services.openssh = {
		enable = true;
		hostKeys = [
			{
				path = "/persist/ssh/ssh_host_ed25519_key";
				type = "ed26619"
			}
			{
				path = "/persist/ssh/ssh_host_rsa_key";
				type = "rsa";
				bits = 4096;
			}
		];
	};
}
```
- ACME certificates (LetsEncrypt): `mkdir -p /persist/var/lib/acme`
```nix
{
	systemd.tmpfiles.rules = [
		"L /var/lib/acme - - - - /persist/var/lib/acme"
	];
}
```

before reboot, list the files on your root filesystem to see if you're missing something important:
`tree -x /`
or `zfs diff rpool/local/root@blank`

- when adding new services, think about the state it is writing
	- if you care about it, find a way to redirect its state to `/persist`