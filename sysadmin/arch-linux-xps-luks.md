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
* It is much easier to setup the bootloader for both OSes when Linux is installed **after** Windows due to how Windows manages the UEFI, so always try and have Windows installed before proceeding to install Linux when dual booting.
* It's possible to install Windows after Linux but it's a much bigger pain than the opposite case.


Initial Install Steps
---------------------

* First, incase you aren't using American English style keyboard layouts, use `loadkeys` to change the layout to something else.
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
    * Hit `Enter` to use the first free sector that `gdisk` detects, or specify another one yourself.
    * This will be a boot partition, so specify a small `+200M` for a 200MB partition to store grub (our bootloader and the linux startup environment).
    * Use code `ef02` to indicate that it is a BIOS boot partition.
    * `p` to print and see if the partition table is correct.
    * Next create the main partition where the root of linux will live.
        * I'm only using a root partition for now till I upgrade my drive.
        * You can use whatever partition scheme you like.
    * `n` to start creating a new partition.
    * `Enter` to select default partition number.
    * `Enter` to use first free sector, or if you know what you're doing specify your own.
    * `Enter` to use remaining free space to determine last sector, or if using other partitions for your scheme, the offset of your root partition.
    * `Enter` to use the default `Linux Filesystem` partition ID.
    * `p` to print out the proposed partition table, verify it's correc
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
    * `cryptsetup luksFormat /dev/nvme0n1p6`
    * You will now be asked to confirm the choice, do so if you're sure.
    * Then you will be asked to create a passphrase for the encrypted drive to create a key system for.
        * **DON'T LOSE THIS PASSPHRASE**.
        * Maybe consider reading my article about more secure passphrases before this step to chose a good password, and how to manage your passwords safely.
        * Enter the passphrase very carefully twice to make sure it matches.
        * Then it will create the partition behind a layer of low level software that encrypts **EVERYTHING** inside.
        * When it's done, triple check the password is good, and create the LUKS cryptographic container that allows interracting with the encrypted partition with a named container, in this case `cryptRoot`.
        * `cryptsetup open --type luks /dev/nvme0n1p6 cryptRoot`.
        * This just mapped the drive to a virtual container that allows reading/writting with the encryption intact called `cryptRoot` inside `/dev/mapper/cryptRoot`.
        * This can be named whatever is preferred.

### LVM

* I'm going to use one big partition for everything linux related that's completely encrypted.
* To make managing how space is divided out and managed within that space easier, I'll be adding LVM as an extra layer of storage management on top of LUKS.
* This isn't necessary, it's entirely possible to skip this step and merely make a filesystem ontop of the encrypted partition taking up the whole space.
* First create the first `pv` or Physical Volume, which is the first layer of storage in LVM, it's simply allocated space that LVM can use to manage the other two layers from a physical set of block devices.
    * `pvcreate /dev/mapper/cryptRoot`
    * `pvdisplay` to verify its creation.
    * This should've created a physical volume across the whole `cryptRoot` LUKS container that was just created.
* Second, create the second layer, `vg` for Volume Group, which is a layer that allows for grouping physical volumes together behind a single namespace, typically with common features such as encryption, SSD vs HDD space, etc.
    * `vgcreate mainGroup /dev/mapper/cryptRoot`
    * This creates a volume group of name `mainGroup`
    * This setup will only use one encrypted partition for the whole linux setup, so there's no point in seperating the space between groups, so there will be just one volume.
    * Verify it has been created with `vgdisplay`
* Third, create the third LVM layer, `lv` for Logical Volume.
    * `lvcreate -l 100%FREE main-vol -n rootVol`
    * This creates a logical volume, which is the last layer in LVM.
    * This can be any number of volumes that appear like block devices to the OS, that can now more easily be resized, and modified.
    * This volume will now be seen as `/dev/mapper/mainGroup/rootVol`.
    * Here it's also possible to not use `100%FREE`, or all the available space in the volume group and to specify volumes of different sizes.
    * I'm sticking with one big root partition for now until I've upgraded the drive to something bigger and I'm more sure about what space will be needed for each major directory.
        * Because LVM is in use, shifting the logical volumes inside `mainGroup` around and resizing them will be much easier.

### Create the Filesystem

* Now there should be a LUKS encrypted partition, it should be mounted as a virtualized container for a block device, and LVM should have mapped partitions from them.
* Time to create an actual filesystem finally, in this case ext4.
* `mkfs.ext4 /dev/mainGroup/rootVol`.
* Then there needs to be a filesystem for the boot partition in `/dev/nvme0n1p5`
    * `mkfs.ext4 /dev/nvme0n1p5`
* Now mount the root filesystem.
    * Later the boot partition will be mounting in it in `/boot`.
    * mount `mount /dev/mainGroup/rootVol /mnt`
* Then mount the boot drive inside the root volume that is now mounted in `/mnt`
    * `mkdir /mnt/boot`
    * `mount /dev/nvme0n1p5 /mnt/boot`
* Verify it was correctly created using `lsblk`.
* It should now show not just the other partitions, but the LUKS one ontop of it, and the lvm volume on top of that.
* Finally, mount the current Windows created EFI in `/dev/nvme0n1p2` so it is accessible to linux.
    * `mkdir /mnt/boot/efi`
    * `mount /dev/nvme0n1p2 /mnt/boot/efi`
    * Verify that this is indeed the EFI partition by checking the contents of `/mnt/boot/efi` for a directory called `EFI` in all caps.
    * If there is an `EFI` directory you know you've got the EFI partition on disk created by Windows, otherwise try one of the other small partitions on disk.
* Note on swap drives.
    * I don't use swap typically, I prefer to trade CPU time over hard drive space in the few cases I need memory than RAM that I have by compressing RAM when it fills up.
    * This computer has 6 cores so it's fairly trivial to compress RAM instead of wearing out the SSD by constantly rewritting data to it.
    * This will be covered in the ***POST INSTALL LINK***.

Install Arch Linux
------------------

* Now that there's internet access, and the drives are setup, it's time to finally install Arch Linux to the drives.
* First, the filesystem needs to be mounted to be worked on.
    * `mount /dev/mapper/main--vol-root /mnt`
* Remember, this is a barebones arch linux install placed on a drive that's being used to carry out all the previous instructions.
* The linux install that's about to be created needs to be seperate from this live linux install that's on the external drive.
* That's why it needs to be mounted while we work on it.
* Before attempting to download all the packages that need installing, make sure that `pacman` is using mirrors that are close to you or you may have a painfully slow install even though only about 300MB of data needs to be downloaded.
    * Edit the file `/etc/pacman.d/mirrorlist` and remove any servers you don't want, typically you don't need more than 6 or so mirrors to chose from to reliably pull packages for pacman
* Next we do the actual installtion by using `pacstrap` which basically downloads and installs any arch package into a given location.
    * `pacstrap /mnt base base-devel neovim`
    * This install `base` & `base-devel` (package groups for arch) into `/mnt` which is the root of the newly created and encrypted filesystem.
    * I include `neovim` as well since I need to edit some configuration files beforing finishing the base install configuration.
    * Add whatever command line editor you prefer.


Base Configurations
-------------------

* Now Arch should be installed, but there's a few things to do before the system is ready to be run and have the many post install tasks that lead to a full modern linux workstation.
* These things still need to be done before we can safely reboot and still get into this OS.
* First the `fstab` or the filesystem table needs to be bootstrapped so that when the system boots into linux it knows where to look to find the OS.
    * `genfstab -U -p /mnt >> /mnt/etc/fstab`
    * Make sure that the encrypted root partition, the boot partition, and the EFI partition have made it into `/mnt/etc/fstab`.
    * `cat /mnt/etc/fstab`
* Now we need to configure Arch through the new installation, which can be done by changing the root to the new installation.
    * `arch-chroot /mnt /bin/bash`
* I use `nvim` to edit text in the command line, so I'm going to install it before configuring anything.
    * Probably should've done this during the `pacstrap`.
    * `pacman -S neovim`.
    * Install whatever text editor you prefer if it isn't installed in `base`.
* Set a root password
    * `passwd` then type the password twice.
    * This will be the password necessary to type before root access is given from now on.
* The locale needs to be specified for various programs to work correctly.
    * Most westerners will use `UTF8`, but there's different locales on top of it that needs specifying.
    * If you are from the US, uncomment `en_US.UTF-8` in this command.
        * `nvim /etc/locale.gen`.
    * Then generate the locale: `locale-gen`.
    * Then create `/etc/locale.conf` and insert `LANG=en_US.UTF-8`.
    * replace `en_US.UTF-8` with whatever locale you need where you are.
* Now set the timezone:
    * first go through the `/usr/share/zoneinfo/` directory and find the filename that represents your timezone best.
    * Then symlink it to `/etc/localtime`
    * `ln -s /usr/share/zoneinfo/America/New_York /etc/localtime`
* Synchronize the hardware clock
    * `hwclock --systohc --utc`
* Now setup the system's host name, this is the name that is visible on the network:
    * In the file `/etc/hostname`:
        * `myhostname`
    * In the file `/etc/hosts`, define the loopback reference to the hostname:
    ```
    127.0.0.1      localhost
    ::1            localhost
    127.0.1.1       myhostname.localdomain
    ```
* Modify InitRAMFS to detect LVM
    * edit `/etc/mkinitcpio.conf` and put `encrypt` & `lvm2` after the preexisting `block` statement in the `HOOKS=` line:
    * `HOOKS="base udev autodetect modconf block lvm2 filesystems keyboard fsck"`
* Generate InitRAMFS using `mkinitcpio -p linux`.
    * This generates the initial RAM disk to boot the initial form of linux before the rest of the linux OS can be booted up.

### Configure the Bootloader

* This is the last really necessary step before the system can be safely rebooted and still be able to use Arch.
* The UEFI should already be installed with Windows 10 in place in the other partition, so we need to use that and tell it to make Linux an option as well.
* It's better to let Windows manage its own information on the UEFI because it can't be made to be more knowledgable of other systems installed on it because... Microsoft.
* So we need to modify the bootloader from here without breaking the Microsoft stuff so both can coexist.
* First install `grub` & `efibootmgr` to install and configure GRUB to point to either install point through UEFI.
    * `pacman -S grub efibootmgr`
* Then mount the EFI: `mount /dev/nvme0n1p1 /boot/efi`
    * Has this been done already?
* Now we can work on the EFI to include the grub bootloader with its own seperate entry for this linux system.
* As you go along it may complain about `lvmetad`, but this is fine for a reason outside the scope of this article.
* Install grub, `grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=grub`
* Generate the initial grub config to be modified later:
    * `grub-mkconfig -o /boot/grub/grub.cfg`
* Install `os-prober` for later use to update grub config to see windows.
    * `pacman -S os-prober`
* Use `blkid` to get the UUID of the LUKS partition so Grub knows to decrypt it on boot up.
    * `blkid | grep /dev/nvme0n1p6 >> /etc/default/grub`
    * This takes the line from the output `blkid` that has information about the block device ID for the encrypted partition and puts it into the grub configuration file to be used later since there's no copy/paste capibility yet.
* Edit `/etc/default/grub`:
    * Uncomment the line about `GRUB_ENABLE_CRYPTODISK=y` to enable decrypting disks.
    * Then change this line to `GRUB_CMDLINE_LINUX="cryptdevice=UUID={PASTE THE UUID THAT WAS PUT INTO THE FILE HERE}:cryptRoot root=/dev/mainGroup/rootVol`:
    * Finally, delete the rest of the line at the bottom that was taken from `blkid`.
* Next modify `/boot/grub/grub.cfg` to look for windows when grub is loaded and present it as an option in the bootloader screen.
```sh
if [ "${grub_platform}" == "efi" ]; then
  menuentry "Microsoft Windows Vista/7/8/10 UEFI-GPT" {
      insmod part_gpt
      insmod fat
      insmod search_fs_uuid
      insmod chain
      search --fs-uuid --set=root $hints_string $fs_uuid
      chainloader /EFI/Microsoft/Boot/bootmgfw.efi
    }
fi
```
* NOTE add the instructions on addng windows to grub



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
