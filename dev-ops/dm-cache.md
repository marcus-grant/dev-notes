How To Setup Tiered Storage Using DM-Cache & LVM
================================================

[Tiered storage][01] is very useful for combining the best features of two storage mediums, namely for this scenario speed of an NVMe drive and the spare space of the remainder of a SATA SSD drive which is slower but has much more space to spare. A faster *hot* tier will sit in front of a slower and typically much larger cold *tier*. The hot tier, will act as a cache, storing the most used files being accessed and speeding them up because chances are most of your accesses are the most used files, read the [Pareto Principle][02]. And if **writeback mode is enabled, writes are are first written to the hot tier and the I/O task is marked as complete and in the background those changes are propagated into the cold tier. In this tutorial we'll be using LVM's default mode of write-through, which caches reads in the hot tier and writes to the cold tier.

> Warning: Writeback caching modes increase the risk of data corruption, because if the hot tier fails through hardware failure, operating system crashes, or premature shutdowns the file being written to might get corrupted. Writeback write failures can be mitigated with write duplication like RAID 1, systems like uninterrupted power supplies that keep the computer on in the event of power failure and snapshot backups.


The Setup
---------

We're starting with this with two relevant partitions, `/dev/nvme0n1p3` & `/dev/sda1`. `nvme0n1p3` is actually a LUKS encrypted partition which gets mapped to `/dev/mapper/cryptHomeHot` on boot, as does `sda1` which gets mapped to `/dev/mapper/cryptHomeCold`. Performing partition en/decryption in linux is out of scope of this article, all you need to know is that those two partitions are decrypted as partitions `cryptHomeHot` & `cryptHomeCold` and otherwise behave just like any other partition. Below is a list of these partitions using the `lsblk` command, *assume from now on* that we're performing all these tasks as root:

```sh
NAME                                     MAJ:MIN RM   SIZE RO TYPE    MOUNTPOINT
sda                                        8:0    0 931.5G  0 disk    
├─sda1                                     8:1    0   652G  0 part    
│ └─cryptHomeCold                        254:3    0   652G  0 crypt 
# ... other block devices & partitions not used here ...
nvme0n1                                  259:0    0 465.8G  0 disk    
├─nvme0n1p1                              259:1    0 300.8G  0 part    
│ └─cryptRoot                            254:0    0 300.8G  0 crypt   /
├─nvme0n1p2                              259:2    0   8.8G  0 part    
│ └─cryptSwap                            254:1    0   8.8G  0 crypt   [SWAP]
└─nvme0n1p3                              259:3    0 156.2G  0 part    
  └─cryptHomeHot                         254:14   0 156.2G  0 crypt
  ```

  Note the names of the partitions, `cryptHomeHot` & `cryptHomeCold`, are the two tiers of storage devices to be used. The hot partition is on a fast NVMe drive, cold on the slower but bigger at 652GiB (~700GB) SATA SSD drive. The root and swap partitions are of no concern here.
  
  
  LVM Setup
  ---------

  A new LVM volume group *(VG)* will be created called `homeVG`, and both the hot and cold tiers will be made part of it. The `cryptHomeHot` physical volume *(PV)*, part of the `homeVG` group, will need to have two logical volumes *(LV)* `homeMeta` & `homeCache` that will handle the hot tier on `cryptHomeHot`. Then the cold tier `cryptHomeCold` only needs one LV, `homeData` which will act as the origin of all files that will be cached by `homeCache` & `homeMeta`. Below is the functional diagram of this desired setup, after it has been mounted to `/home`:

  ![Diagram of DM Cache Setup][dm-cache-diagram]

So to summarize these changes need to be made:

1. Add these physical volumes **(PV)**:
    - `/dev/mapper/cryptHomeCold` from the encrypted NVMe drive
    - `/dev/mapper/cryptHomeHot` from the encrypted SATA drive
2. Add the physical volumes to the volume group `homeVG`
3. Create these logical volumes in `homeVG`:
    - `homeMeta` through physical volume `cryptHomeHot`
    - `homeCache` through physical volume `cryptHomeHot`
    - `homeData` through physical volume `cryptHomeCold`
4. Set `homeMeta` & `homeCache` as a cache to `homeData`
5. Format `homeData`
6. Set `/etc/fstab` to mount `homeData` to `/home` on boot
    - **WARNING** make sure the contents of old `/home` directory have been copied to `homeData` first


Create Physical Volumes for Both Tiers
--------------------------------------

Create physical volumes for the hot and cold tier storage devices using `pvcreate`:

```sh
pvcreate /dev/mapper/cryptHomeCold
pvcreate /dev/mapper/cryptHomeHot 
```

Replace `/dev/mapper/cryptHomeCold` & `/dev/mapper/cryptHomeHot` with the respective cold and hot storage devices for your specific system. This includes non-encrypted and regular partitions like `/dev/sda3` or `/dev/nvme0n1p3`. Then verify that they've been created using the command `pvdisplay` to display the current physical volumes, it should look something like this:

```sh
pvdisplay
"/dev/mapper/cryptHomeCold" is a new physical volume of "<652.00 GiB"
--- NEW Physical volume ---
PV Name               /dev/mapper/cryptHomeCold
VG Name               
PV Size               <652.00 GiB
Allocatable           NO
PE Size               0   
Total PE              0
Free PE               0
Allocated PE          0
PV UUID               RWTsKu-wBP4-KOyS-Pn2j-JWaj-B3TI-Qhlgea

"/dev/mapper/cryptHomeHot" is a new physical volume of "156.17 GiB"
--- NEW Physical volume ---
PV Name               /dev/mapper/cryptHomeHot
VG Name               
PV Size               156.17 GiB
Allocatable           NO
PE Size               0   
Total PE              0
Free PE               0
Allocated PE          0
PV UUID               miczhZ-CH81-bwrP-5Rv6-0d4X-iirF-oHtfKy
```


Add Hot & Cold Tier Physical Volumes to Same Volume Group
---------------------------------------------------------

Next, the hot & cold tiers need to be in the same volume group, in this case it'll be called `homeVG` since it will be used for mounting the `/home` directory, name it whatever seems most appropriate in other setups. To do this, use the `vgcreate` command followed by the group name, then the physical volumes to be added to it:

```sh
vgcreate homeVG /dev/mapper/cryptHomeCold /dev/mapper/cryptHomeHot
```

Again, change `homeVG` to be whatever volume group name is going to be used, and the physical volumes with whatever ones were created with the `pvcreate` command used previously. To verify that this worked, use the `vgdisplay` command to verify information like its `VG Name` and that its `VG Size` is the same as the sum of its physical volumes, like below:

```sh
vgdisplay
  --- Volume group ---
  VG Name               homeVG
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               <808.17 GiB
  PE Size               4.00 MiB
  Total PE              206891
  Alloc PE / Size       0 / 0   
  Free  PE / Size       206891 / <808.17 GiB
  VG UUID               Mer5qQ-YF3P-pIpL-nrvx-USLh-i1Qc-0tGemHreate homeVG /dev/mapp
```


Create Logical Volumes for the Cache and Data 
---------------------------------------------

With both the hot and cold tiered physical volumes inside the same volume group, they can have logical volumes made for the three needed functions for LVM to cache frequently used data in the hot tier, while keeping a copy of all data in the cold tier. The three needed logical volumes, like in the image shown previously are, a cache metadata volume, a cached data volume, and the actual data volume that will hold all data to be cached. It's important to know, *all data will always exist on the cold tier data volume*, if the cache/hot tier gets removed that data will still be accessible on the slower cold tier.

Unfortunately, a little bit of math is advised here to set up the cache volumes. The hot tier's cache will need to maintain metadata of the data chunks that will remain in or be demoted from the cache if they're not accessed enough. The size of the metadata volume, according to this useful [Red Hat LVM mailing list response][03] can be calculated as a proportion of the cache/hot tier size.

To calculate the number of blocks to use for the cache volume, divide the cache size with its block size: `cache_size / block_size`. Each block in the cache volume needs 64 bits to store each block's metadata and a good rule of thumb rule to address this is `4MB + (16 bytes * number of blocks)`. So to do this it's necessary to find the block size of the cache physical volume with `blockdev` command:

```
blockdev --getbsz /dev/mapper/cryptHomeHot
4096
```

Since we know from the earlier `pvdisplay` of `/dev/mapper/cryptHomeHot` that its size is 156.17GiB (167 GB) and its block size is 4096 bytes the number of blocks are `167,000,000,000 bytes / 4096 byte blocks` is `40,770,000` blocks rounded up to the next thousand be safe that there's enough metadata. Then using the rule of thumb; `4MB + (16 byte * 38,128,000 blocks)` is `656,320,000` bytes for metadata, let's call it `660MB` to be even more safe and to have a clean number.

Now create a `660MB` sized metadata logical volume on the cache physical drive `/dev/mapper/cryptHomeHot`, replace the names for the system being worked on and use the command `lvcreate`:

```sh
lvcreate -L660M -n homeMeta homeVG /dev/mapper/cryptHomeHot
lvcreate -l 100%FREE -n homeCache homeVG /dev/mapper/cryptHomeHot
```

These are more complex commands, so let's summarize:
- `-L660M` specifies the size in absolute terms, the `660M` calculated for the metadata volume
- `-n homeMeta` just specifies that the volume should be name `homeMeta`
- `homeVG` specifies which volume group to put the volume in
- `/dev/mapper/cryptHomeHot` when the last argument of `lvcreate` specifies which physical volume to use for this logical volume
    - Since this is for the cache tier, it needs to be the faster physical volume
- `-l 100%FREE` uses the `-l` size specifier which allows relative sizes, in this case all the remaining space in `cryptHomeHot`
- Everything else is the same for `homeCache` except of course its name

Verify the two cache volumes with `lvdisplay`:

```sh
  --- Logical volume ---
  LV Path                /dev/homeVG/homeMeta
  LV Name                homeMeta
  VG Name                homeVG
  LV UUID                8oTHo6-FmWm-6qrT-JxhQ-WjX9-87u6-rrTpyA
  LV Write Access        read/write
  LV Creation host, time thor, 2019-10-16 02:29:04 -0400
  LV Status              available
  # open                 0
  LV Size                660.00 MiB
  Current LE             165
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           254:15
   
  --- Logical volume ---
  LV Path                /dev/homeVG/homeCache
  LV Name                homeCache
  VG Name                homeVG
  LV UUID                g2PoP1-jdTJ-gvq1-D3VN-BlJW-rcn8-M6QGfW
  LV Write Access        read/write
  LV Creation host, time thor, 2019-10-16 02:33:19 -0400
  LV Status              available
  # open                 0
  LV Size                <155.53 GiB
  Current LE             39815
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           254:16
  ```

  Now for the cold tier volume `homeData`:

  ```sh
  lvcreate -l 100%FREE -n homeData homeVG /dev/mapper/cryptHomeCold
  ```

  Same as before, `100%FREE` to use all space left in `cryptHomeCold`, and for this volume of course the `cryptHomeCold` slower and larger physical volume gets used. Then name is something to represent the actual backing store of data, in this case `homeData`. Verify again with `lvdisplay` and make sure the size, volume group are correct then continue.


Create the Cache Pool to Cache the Data Volume
----------------------------------------------

Then to actually create the tiered caching system; `homeMeta` & `homeCache` need to be combined into something LVM refers to as a `cachepool` for `homeData`. The cache pool manages the movement of data between the `homeCache` and `homeData` volumes using `homeMeta` to store information about the `homeCache` so LVM knows which blocks of data are used enough to warrant being stored in `homeCache`.

To do this, use the `lvconvert` command like this:

```sh
lvconvert --type cache-pool --cachemode writethrough \
--poolmetadata homeVG/homeMeta homeVG/homeCache
```

If successful it should tell you what LVM thinks is the optimal chunk size, generally a good idea to follow it, then to confirm the operation because it will wipe the cache volumes. The command above has `--type cache-pool` to convert the logical volumes for use as cache devices. Then `--cachemode writethrough` means that writethrough caching will be used, *i.e. write to the cold tier & read from the hot tier first*. If writeback mode is preferred, knowing there are some minor risks involved then replace `writethrough` with `writeback`. Finally, `--poolmetadata homeVG/homeMeta homeVG/homeCache` is where the pool specifies which volume in the pool will store its metadata, in this case `homeVG/homeMeta` stores metadata for `homeVG/homeCache` and its necessary to specify for each volume which group it comes from.

Then to attach the newly created cache pool `homeVG/homeCache` to the backing storage volume, `homeVG/homeData` use `lvconvert` again but this time of type `cache`:

```sh
lvconvert --type cache --cachepool homeVG/homeCache homeVG/homeData
```

Now the entire cache pool is used to cache its backing volume `homeData` after asking to confirm the operation because the cache pool needs to be wiped before continuing. Now it's important to learn a new command to verify more complex volume configurations in LVM, `lvs -a`:

```sh
lvs -a
# results in:
  LV                VG     Attr       LSize    Pool        Origin           Data%  Meta%  Move Log Cpy%Sync Convert
  [homeCache]       homeVG Cwi---C--- <155.53g                              0.01   1.02            0.00            
  [homeCache_cdata] homeVG Cwi-ao---- <155.53g                                                                     
  [homeCache_cmeta] homeVG ewi-ao----  660.00m                                                                     
  homeData          homeVG Cwi-a-C---  651.35g [homeCache] [homeData_corig] 0.01   1.02            0.00            
  [homeData_corig]  homeVG owi-aoC---  651.35g                                                                     
  [lvol0_pmspare]   homeVG ewi-------  660.00m                                                                     
```

If it looks something like this, then it's in place and working. There's a few extra hidden volumes LVM uses internally on the cache and cache pool systems to keep everything running, that's normal. What's most important is to check the size of `homeCache` and `homeData`, or whatever their respective names are supposed to be. It can also be useful when these drives fill up to check their respective statistics `Data%` & `Meta%` to see how they're being used respectively.


Format the Cached Volume
------------------------

The cached volume isn't going to be useful without putting a filesystem on top of it, that can then be mounted for the operating system to use. In this case, it's going to be ext4, but it could be any supported filesystem like ext3, f2fs, xfs, etc. It would be inadvisable however to use managed filesystems on it however like btrfs or zfs because they need to be able to manage the entire stack of the storage system by themselves to work optimally and not cause conflicts. Use whatever the make filesystem command is for that filesystem, in this case ext4 is created with:

`mkfs.ext4 /dev/homeVG/homeData`


Mount the Volume
----------------

Now that the drives are cached using LVM and have an associated filesystem, it's time to mount it to actually do some input and output with it:

```sh
mkdir /mnt/tmp
mount /dev/homeVG/homeData
```

It's now mounted to `/mnt/tmp`. If it's not going to be used to replace a top level directory that linux needs, *i.e. anything mounted directly on root like /home, /var, /usr, etc.* all that's needed is to just mount it wherever it's needed and finish this tutorial. Since this volume is meant to be used as a larger and faster home directory; it's necessary to first copy the current contents of `/home` into the new cached filesystem, move the old `/home` to a temporary directory to be deleted later, then modify `/etc/fstab` to mount it at boot.

### Copy the Data of Mount Point to Be Replaced (Optional)

With the cached filesystem mount to a temporary location, like `/mnt/tmp`, copy the contents of the mount point the cached filesystem will replace:

`rsync -av --info=progress /home/ /mnt/tmp`

Summary of options:
- `-a` is rsync's *archive* mode
    - preserves all metadata of all files including user, group, permissions, timestamps, etc.
    - preseves symlinks and hardlinks
- `-v` is just for verbose mode so it will print more information as it's copying
- `--info=progress` prints out even more information while copying related to overall progress
- `/home/` is the source directory to be copied
    - the trailing `/` on `/home` indicates rsync should copy the contents of `/home`, but not the parent directory itself
    - this is important because `/home` is where the volume will be mounted
- `/mnt/tmp` is just the source directory where this will be copied to
    - this with `/home/` will make the cached drive mounted at `/mnt/tmp` look exactly like `/home` on the file level

### Change FSTAB to Mount Cached Volume on Boot (Optional)

To make sure crucial top level directories like `/home` are mounted on boot, it's necessary to edit `/etc/fstab` to mount the cached volume to its new mount point when rebooted. It should look something like this:

```
# ... other partitions
/dev/homeVG/homeData            /home   ext4    defaults 0 2
# ... even more partitions
```

Be sure that if there's another `fstab` record for the mount being replaced that it has been commented out or deleted. Replace all the values for the specific partition names and mount points specific to the system. Also note, that if this cached volume is built on top of any encrypted drives *like mine*, then it's necessary to also edit `/etc/crypttab` so on boot the operating system knows how to decrypt it. Otherwise it will be necessary to enter the encryption key manually on every boot. Editing `/etc/crypttab` is out of the scope here though, but there's tons of resources online to find out how.

###  Move the Original Mount Point Somewhere Else In Case of Problems (Optional)

To make sure that an easier recovery from a mistake is possible when the computer is rebooted, move the original directory somewhere else or give it a different name. This is also important because when the new device is mounted on top of it, the old device storing this location's data will still take up that space, but it won't be accessible with the new device mounted there.

> WARNING: This might not be possible on a running system. It's advisable to do this from a fallback shell or with a live USB to perform the final swap. In this case, since it's only /home being moved, it's possible to logout of the GUI and go into a TTY shell using the key combination Ctrl+F1 or any other F-key to F7. Moving /var or /usr might not be possible on a running system.

Before continuing make sure a recovery method is ready, like a live USB of linux that can be booted into to fix any mistakes made. They're relatively easy problems to fix so don't worry too much. To move the old mount point, to make way for the new one:

```sh
mv /home /old-home
```

### Reboot

Hopefully now it's possible to reboot and still come back to the operating system with the new mount location automatically applied to the newly cached volume.





References
----------

1. [Wikipedia: Memory Hierarchy][01]
2. [Wikipedia: Pareto Principle][02]
3. [Red Hat Mailing List: dm-cache cache target][03]

[01]: https://en.wikipedia.org/wiki/Memory_hierarchy#Application_of_the_concept "Wikipedia: Memory Hierarchy"
[02]: https://en.wikipedia.org/wiki/Pareto_principle "Wikipedia: Pareto Principle"
[03]: https://www.redhat.com/archives/dm-devel/2012-December/msg00046.html "Red Hat Mailing List: dm-cache cache target"

[dm-cache-diagram]: ./pics/dm-cache-diagram-v1.png
<!-- [dm-cache-diagram]: ./pics/dm-cache.svg -->

