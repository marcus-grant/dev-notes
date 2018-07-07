Create the Install Medium
-------------------------

* If you're on a unix based system (WSL doesn't count due to lack of drive access), good ole' `dd` works great, it just doesn't look very fancy or give you particularly useful prompts.

```sh
dd bs=4M if=~/Downloads/arch-linux-2018.07.01-dual.iso of=/dev/sdX status=progress && sync
```

* Otherwise, the crossplatform and easier, *(though in my experience, slightly less reliable)*, approach is to use [etcher][03].
    * Super easy, just choose the image to flash, and the drive to flash it to, and done.
    * Though I've found it's slightly more likely to produce a badly flashed drive than just `dd`, but the user experience means that unless a command line is already open on my system, I'll probably use etcher instead.


Prepare Windows for Dual Booting
--------------------------------

* There are a few features that windows puts into modern UEFIs that make dual booting with linux much more difficult.
* Disable these features:
    * Disable secureboot
    * Disable or change fast boot to `thorough` mode.
* By default the whole disk is formatted for windows, plus the first partition ***(DO NOT DELETE THIS, IT'S THE UEFI partition)***, and some recovery partitions. As you'll see later, the recovery partitions might be useful to you so keep it around till at least after the dual OSs are setup then delete them.
* Shrink the windows partition down by at least 32GB to make room for linux.
    * These days I can't really recommend less then 32GB for linux unless it's being used for a very specific or niche reason.
    * Obviously have even more space available if you're doing more stuff with the Linux install, particularly if you'll be using media like video.
* In the XPS 15 these options are available in BIOS
* Now, there's a problem with the way the XPS line of laptops use their SSDs
    * There's an odd hardware RAID driver option that's on by default, for some stupid reason...
    * What this means is that the Intel Rapid Store driver must already be installed on the boot section of the OS, which linux doesn't have and I'm not aware of any kernel modules that allow it.
    * So the problem is, with this option disabled windows isn't recognized anymore. So either a fresh install of windows with the option off needs to be made first, or...
    * The original instalation needs to be cloned, maybe try clonezilla, and then restore from the clone after switching `RAID` to `AHCI`.
    * Dell, seriously, why do this, `AHCI` is both faster and more reliable and there's only one storage slot available...
* Anyways... hopefully you're reading this having already decided to dual boot linux on a fresh machine and you've already made a windows install medium.
* When all that mess is done, put the drive into the computer, reboot and keep tapping `F12` as it reboots to enter the boot selection mode, then choose the newly flashed drive to boot from.


Initial Install Steps
---------------------

* First, verify that boot mode is using UEFI, after having windows installed I can't imagine a reason why it wouldn't be, but check to be sure.

```sh
ls /sys/firmware/efi/efivars
```

* This should return a huge list of EFI files, if it does the UEFI is definitely installed and working.
* Unless an ethernet adapter is in use, wifi will be needed to be configured to continue the arch install.
* First check that the device is detected: `lspci -v`
    * This gets information about all PCI connected devices in detail
    * The default WiFi card on the XPS 15 is the `Qualcomm Atheros QCA6174`
    * If you have something else, look for wording in each entry suggesting it's a WiFi adapter of some kind.
    * Find the `Kernel modules:` line and remember what the name of the kernel module is.
    * For me it was, `ath10k_pci`
* Then check if the device was properly loaded with: `dmesg | grep {MODULE_NAME}`.
* Look for anything hinting that the interface was setup.
* For me, this gave it away:
    * `ath10k_pci 0000:3b:00.0 wlp59s0: renamed from wlan0`
    * Now we know there's a wireless interface named `wlp59s0` that can be used.
* Sometimes new wireless drivers or modules will need to be bootstrapped somehow, but one benefit of arch is you get the absolute latest mainline kernels by default so this WiFi adapter already has drivers and kernel modules.
* Make sure that the interface is enabled: `ifconfig wlp59s0` *or insert your interface name, which is likely to be different even if you have the same adapter*.
* There's a lot of nuance in connecting to different command lines and configuring each setting yourself on the command line.
* Fortunately the arch install media comes with `wifi-menu`.
* The command line interface is pretty easy to use to first setup a profile for your connection to the access point, and then connect to it.
* Verify the connection works by pinging, `ping google.com`.
* This should also exist after the base install of arch as it's part of the `netctl` package which is itself part of the `base` group of packages installed with the rest of arch.
* `wifi-menu` is basically just a nice interface over `netctl`.

* Next make sure the system clock, `timedatectl set-ntp true`
* ***TODO make sure that there's mention of keeping windows and linux clocks from conflicting with each other which tends to happen in dual boot***


Setup the Partitions
--------------------

* I am going to encrypt my hard drive at the block-level using LUKS & [dm-crypt][04].
* [Full disk encryption][05] is very useful if there's anything at all that's stored on the disk that you'd rather not have other people see if the computer is stolen.
    * It's very likely that if you do work tasks on your computer that that you're liable to any information that gets exposed from your laptop being stolen.
    * It won't help if you get hacked over the internet.
    * There's cold boot hacks that are supposedly possible by state agencies and really skilled hackers but there isn't much information about them that's even remotely approachable to even skilled computer users, but that's a possible subversion.
* First it's necessary to know which block device reference to use.
* This was a surprise to me as this was the first NVMe device I've played around with and I would've screwed this up if I didn't check first.
* Check what block devices are visible with `lsblk`.
* By default the XPS 15 uses an NVMe drive, and will probably be called `nvme0n1`.
* Now the namespace for block devices when using NVMe's new storage protocol there's a new naming convention in linux.
* Then format the free space that was created before in Windows by using `gdisk /dev/nvme0n1`.
    * `p` prints out the current partitions, make note of the `Start` & `End` sectors in the partition table that's printed.
    * These sectors are important to make sure you aren't going over sectors like the ones in the recovery partitions.
    * Here, if you decide to you can remove the recovery partitions
    * You can still do anything you need them to do if you have access to a another computer with Windows installed to create new install media incase you mess up.
    * The serial number for your built in windows license on XPS laptops is built into the ROM of the BIOS so no matter what, if you install Windows 10 even if you install it on a fresh new drive, you will still be able to use your windows license.
    * Create a new partition using `n`.
    * Hit `Enter` to use the default partition number.
    * Use the first sector `gdisk` gives you to start the partition, by hitting `Enter`.
        * **However**, if you have partitions like the recovery ones after the newly created free space after the shrinked windows partition, make sure the ending sector doesn't overlap with the recovery ones if you want to preserve them.
    * Tell what size the partition should be by using this syntax: `+32G`.
        * The plus tells it to add sectors by a size in bytes.
        * `32` is the number of a unit of bytes.
        * `G` represents gigabytes, could also be `M`, or `T`, or even `k` for kilobytes, but I doubt anyone needs to make a partition that small anymore.
    * Now the program should know the location of the partition, just make sure by hitting `p` to verify the new partition table.
    * When it looks correct, hit `w` to write the changes to disk.

### Encrypt Using LUKS

* Now with a partition mapped out for linux, if you want encryption continue with these instructions, otherwise skip to the file system creation steps.
* Encrypt the root partition using `cryptsetup`.
    * `cryptsetup luksFormat /dev/nvme0n1p5`
    * You will now be asked to confirm the choice, do so if you're sure.
    * Then you will be asked to create a passphrase for the encrypted drive to create a key system for.
        * **DON'T LOSE THIS PASSPHRASE**.
        * Maybe consider reading my article about more secure passphrases before this step to chose a good password, and how to manage your passwords safely.
        * Enter the passphrase very carefully twice to make sure it matches.
        * Then it will create the partition behind a layer of low level software that encrypts **EVERYTHING** inside.
        * When it's done, triple check the password is good, and create the LUKS cryptographic container that allows interracting with the encrypted partition with a named container, in this case `cryptRoot`.
        * `cryptsetup open --type luks /dev/nvme0n1p5 cryptRoot`.
        * This just mapped the drive to a virtual container that allows reading/writting with the encryption intact called `cryptRoot` inside `/dev/mapper/cryptRoot`.
        * This can be named whatever is preferred.

### LVM

* I'm going to use one big partition for everything linux related that's completely encrypted.
* To make managing how space is divided out and managed within that space easier, I'll be adding LVM as an extra layer of storage management on top of LUKS.
* This isn't necessary, it's entirely possible to skip this step and merely make a filesystem ontop of the encrypted partition taking up the whole space.
* First create the first `pv` or Physical Volume, which is the first layer of storage in LVM, it's simply allocated space that LVM can use to manage the other two layers from a physical set of block devices.
    * `pvcreate /dev/mapper/crypt-root`
    * `pvdisplay` to verify its creation.
    * This should've created a physical volume across the whole `crypt-root` LUKS container that was just created.
* Second, create the second layer, `vg` for Volume Group, which is a layer that allows for grouping physical volumes together behind a single namespace, typically with common features such as encryption, SSD vs HDD space, etc.
    * `vgcreate main-vol /dev/mapper/crypt-root`
    * This creates a volume group of name `main-vol`
    * This setup will only use one encrypted partition for the whole linux setup, so there's no point in seperating the space between groups, so there will be just one volume.
    * Verify it has been created with `vgdisplay`
* Third, create the third LVM layer, `lv` for Logical Volume.
    * `lvcreate -l 100%FREE main-vol -n root`
    * This creates a logical volume, which is the last layer in LVM.
    * This can be any number of volumes that appear like block devices to the OS, that can now more easily be resized, and modified.
    * This volume will now be seen as `/dev/mapper/main-vol/root`.
    * Here it's also possible to not use `100%FREE`, or all the available space in the volume group and to specify volumes of different sizes.
    * I'm sticking with one big root partition for now until I've upgraded the drive to something bigger and I'm more sure about what space will be needed for each major directory.

### Create the Filesystem

* Now there should be a LUKS encrypted partition, it should be mounted as a virtualized container for a block device, and LVM should have mapped partitions from them.
* Time to create an actual filesystem finally, in this case ext4.
* `mkfs.ext4 /dev/mapper/main--vol-root`.
* Verify it was correctly created using `lsblk`.
* It should now show not just the other partitions, but the LUKS one ontop of it, and the lvm volume on top of that.


Install Arch Linux
------------------

* Now that there's internet access, and the drives are setup, it's time to finally install Arch Linux to the drives.
* First, the filesystem needs to be mounted to be worked on.
    * `mount /dev/mapper/main--vol-root /mnt`
* Remember, this is a barebones arch linux install placed on a drive that's being used to carry out all the previous instructions.
* The linux install that's about to be created needs to be seperate from this live linux install that's on the external drive.
* That's why it needs to be mounted while we work on it.
* Next we do the actual installtion by using `pacstrap` which basically downloads and installs any arch package into a given location.
    * `pacstrap /mnt base base-devel`
    * This install `base` & `base-devel` (package groups for arch) into `/mnt` which is the root of the newly created and encrypted filesystem.




References
----------

[01]: https://wiki.archlinux.org/index.php/Installation_guide "Arch Wiki: Installation Guide"
[03]: https://etcher.io/ "Etcher.io"
[04]: https://wiki.archlinux.org/index.php/dm-crypt "Arch Wiki: dm-crypt"
[05]: https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system "Arch Wiki: Encrypting Entire System"

1. [Arch Wiki: Installation Guide][01]
3. [Etcher.io][03]
4. [Arch Wiki: dm-crypt][04]
5. [Arch Wiki: Encrypting Entire System][05]
