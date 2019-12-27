Stratis Next-Generation Storage Notes
=====================================


Some Reasons to Use Stratis
---------------------------

Stratis is a VMF or *Volume Management Filesystem* being developed by Red Hat, and which is now stable enough to start being used, *though you should have backups in place, just in case due to its young age*.

BTRFS & ZFS are two other next generation filesystems that were promising to take over the past ones but both have some issues of their own. BTRFS seems to be perpetually a year away from stability, and requires far too much maintenance by administrators to remain reliable. ZFS is a pretty stunning filesystem, but rather complex to manage and can be too burdensome on the  machine that runs it to get some of its best features. Also, it can't be mainlined into linux due to the fact that Oracle owns the rights to it after they bought out Sun Microsystems.

These are problems for a lot of Linux System Administrators and Red Hat decided that though their own LVM which can do some of these things, it was time for a new system designed from the ground up to take on these two Volume-based filesystems with a better management interface, a more kernel-integrated set of modules and utilities to do the actual managing of storage devices, and a more modular design than BTRFS that lets administrators layer new features on top of their stratis storage systems as they're needed instead of the more monolithic BTRFS & ZFS designs. This adheres more closely to the [UNIX Philosophy][04] *i.e.* greater modularity in software and simplifying each module as much as possible.

Some of the features that Stratis supports right now:
    - File-system snapshot
    - Thin provisioning
    - Tiering
    - Storage device pooling
    - Volume management layered on top of storage pools (like LVM)

[OpenSource.com][03] has a great two-part article that covers what Stratis is in greater detail, why Red Hat chose to start fresh with it, and what it will provide that ZFS & BTRFS can't.

Stratis Volume Components
-------------------------

> Stratis provides the following components to the administrator through its command line interface or through API access:

- **blockdev**
    - "block devices"
    - i.e. disks, disk partitions, disk containers like LVM, LUKS, MDRAID, etc.
    - analogous to *physical volumes* on LVM
- **pool**
    - the **pool** of **blockdev**ices which aggregates them into one named group
    - fixed total size, the sum of all its constituent **blockdevs**
    - contains most of the useful features of stratis like dm-cache for caching
    - creates a `/dev/stratis/POOL_NAME` entry for each pool
    - analogous to *volume group* on LVM
- **filesystem**
    - each pool can have multiple **filesystems**
    - these act just like any other file system, files come in, files come out
    - these are the mount points to the operating system
    - Stratis only uses XFS as the filesystem format

Supported Block Devices
-----------------------

- LUKS (encrypted block device containers)
- LVM logical volumes
- MD RAID
- DM Multipath
- iSCSI
- Hard & Solid-State Drives
- NVMe Drives

> WARNING: Redundancy
> Currently (v1.0.5 as of writing) doesn't support device failures by itself yet. If it's a multiple block device pool, one failure will bring down the whole thing for now. If you need redundancy, rely on external backups or MDRaid or iSCSI to provide it, not Stratis.

> WARNING: Thin Provisioned Block Devices
> Because Stratis itself uses thin-provisioning, if block devices that make up its pools are themselves thin provisioned will create destructive conflicts, DO NOT use already thin-provisioned block devices.

Install Stratis
---------------

- Only available as a package on Red Hat based operating systems *(CentOS 8+, Fedora 29+, RHEL)*, and on arch based ones for now
- Other operating systems will probably need to built manually

### Procedure for Red Hat Distributions

1. Install packages providing stratis daemon & service along with the command-line tool that helps manage the service:

```sh
sudo yum install stratisd stratis-cli
```

2. Ensure `stratisd` service is enabled & running if planning on using it now:

```sh
sudo systemctl enable --now stratisd
```

### Procedure for Arch Distributions

1. Install packages providing stratis daemon & service along with the command-line tool that helps manage the service:

```sh
sudo pacman -S stratisd stratis-cli
```

2. Ensure `stratisd` service is enabled & running if planning on using it now:

```sh
sudo systemctl enable --now stratisd
```

Create a Stratis Pool
---------------------

> It is assumed the reader knows how to use 'sudo' or 'su' to escalate privileges to root as needed to perform disk operations like this. Assume every command from now on is carried out by a root, or root-like permitted user.

1. First wipe the block device of any pre-existing filesystems:

```sh
wipefs --all /dev/SOME_BLOCKDEV
```
Replace `SOME_BLOCKDEV` with a valid block device like `/dev/sdb` or `/dev/mapper/some-encrypted-drive`. This includes a LUKS partition, which should be created before any of this if encryption is desired.

2. Create stratis pool on the device

3. Verify it worked with command `stratis pool list`:

```sh
Name     Total Physical Size  Total Physical Used
bigPool             1.82 TiB             1.89 GiB
```

You can also verify it with `lsblk`:
```sh
sdb                                              8:16   0   1.8T  0 disk
└─sdb1                                           8:17   0   1.8T  0 part
  └─cryptBigHDD                                254:2    0   1.8T  0 crypt
    └─stratis-1-private-e8b98e13ad6f420c940340cd35f385c3-physical-originsub
                                               254:3    0   1.8T  0 stratis
      │-─stratis-1-private-e8b98e13ad6f420c940340cd35f385c3-flex-thinmeta
      |                                        254:4    0   1.9G  0 stratis
      │ └─stratis-1-private-e8b98e13ad6f420c940340cd35f385c3-thinpool-pool
      │                                        254:7    0   1.8T  0 stratis
      ├─stratis-1-private-e8b98e13ad6f420c940340cd35f385c3-flex-thindata
      │                                        254:5    0   1.8T  0 stratis
      │ └─stratis-1-private-e8b98e13ad6f420c940340cd35f385c3-thinpool-pool
      |                                        254:7    0   1.8T  0 stratis
      └─stratis-1-private-e8b98e13ad6f420c940340cd35f385c3-flex-mdv
```

It will show a bunch `stratis` type partitions that represent each `blockdev`'s layered storage devices that stratis uses to implement the various features like thin provisioning and what-not. In my case, I have a LUKS encrypted drive partition on `/dev/sdb1` opened as `/dev/mapper/cryptBigHDD`. The **blockdev** in this pool is `cryptBigHDD`, meaning all its contents are encrypted, then on top of that stratis manages the data.


Add Another Block Device to a Pool (Optional)
---------------------------------------------

To show how to add more **block devices** to the pool, and show off another nice feature of stratis, let's add another **blockdev** to the pool. This one will not actually add to the total storage of the pool, instead it will add to its speed. That's because stratus supports [storage tiering][05], which basically means a fast **blockdev** like an NVMe or SATA SSD can be pooled with a bigger, slower spinning rust HDD. This means you get the storage space of the HDD, but because the SDD stores the most read files, and all writes to the pool before being sent out to the backing HDD you get most of the SSD's speed as well.

Add devices to pools by using `stratis pool add-data POOLNAME DEVICE` or in this case add a cache tiered device with `stratis pool add-cache POOLNAME DEVICE`. Then verify with `stratis pool`:

```sh
Name     Total Physical Size  Total Physical Used
bigPool             1.82 TiB             1.89 GiB
```

Which doesn't show much, just my same `bigPool` pool, which may or may not have the new cache disk. It should be increasing in size if the `stratis pool add-data` command was used, but because a cache tier device is added, look at the `blockdev`s using `stratis blockdev`:

```sh
Pool Name  Device Node              Physical Size  State   Tier
bigPool    /dev/mapper/cryptBigHDD       1.82 TiB  InUse   Data
bigPool    /dev/mapper/cryptBigSSD     279.51 GiB  InUse  Cache
```

Now the slow and large `cryptBigHDD` of size 1.82TiB (2 Terabytes) is being cached by `cryptBigSSD` which is much faster and can hold 279.51Gib (300 Gigabytes). This means about 1/6 of the storage space of the big slow drive is used to read the most used files without waiting on the hard disk. Also part of that space is used to temporarily hold changes that need to be written to the slow disk meaning writes will be sped up as well and eventually be moved to the slow disk.


Create a Stratis File System
----------------------------

Now let's create an actually usable filesystem out of this pool.

1. Create the filesystem `some-fs` on pool `some-pool`:

```sh
stratis fs create some-pool some-fs
```

2. Verify the newly created filesystem on `some-pool`:

```sh
stratis fs list some-pool
Pool Name  Name   Used     Created            Device                            
bigPool    bigFS  546 MiB  Oct 15 2019 02:44  /stratis/bigPool/bigFS  
```

And that's the filesystem, named `bigFS`. Note that only 546MiB are in use, this is because this is a thin provisioned system, meaning it will be as large as it needs to be. If another filesystem is added, both will grow till they can't anymore so sizing doesn't need to be managed till full, or quotas are set. What is in there now is just pre-provisioned space that will be larger than the actual amount used due to how thin-provisioning works.


Mount Stratis File Systems
--------------------------

The last thing before a stratis-managed filesystem is usable is to actually mount it somewhere. All stratis pools & filesystems are mapped in linux in the root top-level directory, following the pattern: `/stratis/pool_name/fs_name`. To mount them it's just like any other filesystem, just use the `mount` command:

```sh
mount /stratis/pool_name/fs_name mount_point
```

Now that it's mounted let's see how it performs by writing test files using the `dd` command:

1. Write a 1GB testfile using `dd` and `/dev/urandom`:

```sh
dd if=/dev/urandom of=/mnt/mount_point/test_in.txt bs=1M count=1000
```

2. Now that the file is written and isn't bound by the speed of `/dev/urandom` run duplicate the file:

```sh
dd if=/mnt/mount_point/test_in.txt of=/mnt/mount_point/test_out.txt
# should result in something like...
2048000+0 records in
2048000+0 records out
1048576000 bytes (1.0 GB, 1000 MiB) copied, 2.65964 s, 394 MB/s
```

The results are taken form my SATA SSD acting as cache to a 2.5" laptop drive acting as a data block device, which in even the best cases can only write at 100 MB/s. So not only is the pooled filesystem reading and writing data as filesystems do, but it's doing so at a much faster rate than the backing hard disk is normally capable. The cache tier is successfully speeding the hard disk behind it!


Make a Stratis File System Mount Permanent
------------------------------------------

To make the filesystem mount during boot up without any manual intervention, it's much the same as any other linux filesystem mounts, just use `/etc/fstab`.

1. Determine the UUID of the stratis filesystem:

```sh
lsblk --output=UUID /stratis/pool_name/fs_name
```

2. If the mount point directory for the filesystem doesn't already exist, create it:

```sh
mkdir -p /path/to/mount/point
```

3. Edit `/etc/fstab` to include a line for the stratis filesystem using the UUID found from before, and use `xfs` as the filesystem type:

```sh
# ... previous fstab records like root, home, etc...
UUID=the-uuid-found-from-lsblk-or-blkid /path/to/mount/point xfs defaults,x-systemd.requires=stratisd.service 0 0
```

The mount options has `x-systemd.requires=stratisd.service` and this is probably new for a lot of linux users. Essentially it indicates to systemd (the linux service/daemon manager & init system), that it needs stratis's `stratisd` daemon to mount it.

4. Update systemd mount units so when the system boots it knows how to deal with stratis as a mount point:

```sh
systemctl daemon-reload
```

5. Then finally, check that the mount point setup through `fstab` is working:

```sh
mount /path/to/mount/point
```









References
----------

1. [Red Hat Docs: Managing Layered Local Storage with Stratis][01]
2. [LinuxForGeek: Using Stratis to Configure Local Storage in RHEL 8 & Fedora][02]
3. [OpenSource.com: Configuring Local Storage in Linux][03]
    - (Good Background Info on Stratis & What Makes It Different)
4. [Wikipedia: Unix Philosophy][04]
5. [Wikipedia: Automated Tiered Storage][05]

[01]: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_file_systems/managing-layered-local-storage-with-stratis_managing-file-systems "Red Hat Docs: Managing Layered Local Storage with Stratis"
[02]: https://linuxforgeek.com/configure-local-storage-using-stratis/ "LinuxForGeek: Using Stratis to Configure Local Storage in RHEL 8 & Fedora"
[03]: https://opensource.com/article/18/4/stratis-easy-use-local-storage-management-linux "OpenSource.com: Configuring Local Storage in Linux"
[04]: https://en.wikipedia.org/wiki/Unix_philosophy "Wikipedia: Unix Philosophy"
[05]: https://en.wikipedia.org/wiki/Automated_tiered_storage "Wikipedia: Automated Tiered Storage"
