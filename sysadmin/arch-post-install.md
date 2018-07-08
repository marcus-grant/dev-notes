Post Install for a Bare Arch Linux OS
=====================================

Now that arch has been installed and sparingly configured enough to reboot correctly from its bootloader it's time to turn it into a useful linux workstation. This was done to create my linux workstation on my XPS 15 (9570) laptop so this guide might be useful for others trying to get Linux working well on the same or similar hardware.


User Setup
----------

* Probably the first thing that should be done is to add yourself as a user seperate from root.
* It's better to prevent more accidental breaking changes to the system and to add a degree of seperation for security reasons.
* Add a new user with: `useradd -m -g initial_group -G additional_groups -s login_shell username`
    * `-m`: creates a home directory for the user.
    * `-g`: defines the first group this user belongs to.
        * If this is the primary login account, consider making this the `wheel` group.
        * Wheel is Arch's default administrators group.
    * `-G`: defines any other groups to belong to.
    * `-s`: specifies which shell to use, I use `/bin/bash`, but ZSH or Fish are other popular choices.
* Then give the user `username` a password with, `passwd username`.
* Then to make `username` able to easily perform root tasks when needed, setup `sudo` to work with this user.
    * For now we'll just give `username` full root access on using `sudo`, more detailed configurations are covered in [here][04] in the Arch Wiki.
    * `visudo`
    * This is a built in editor to edit sudo, I'm not sure why it does this but I would have to guess it has to do with securing access to sudo.
    * Go the section mentioning `## User privelege specification`
    * Add the line: `username ALL=(ALL) ALL`
    * This gives `username` root access when using `sudo`.


Graphics, Display Managers & Desktop Environments
-------------------------------------------------

* This is where Arch will start to feel like a full fledged OS.
* Desktop environments are software packages that create a full User Interface system that most people will be familiar with when using computers.
* I mostly use `i3wm`, but that is only a window manager and doesn't come with much nice to have software that can also be used with `i3wm`.
* Because `i3wm` is so bare bones, I usually start by installing Gnome, my preferred Desktop Environment for when I want a fully featured UI.
* There are tons of extras that come with Gnome that are useful in simpler environments like `i3wm` including a display manager, so that's why I start with a more fully featured desktop environment first.
* You can install any number of alternatives listed [here][05].
* Before installing `gnome` however, there needs to be a graphics server installed and today there's two options: `xorg` & `wayland`.
* `xorg` is the old standard that is much more stable, fully-featured, and well-supported in the community.
* `wayland` is up and coming and will probably supercede `xorg` someday, but for now I'd rather not deal with the headaches of missing features and desktop/window-manager problems associated with using it.
* Use `xorg` unless you really know what you're doing.
* Install `xorg` with `sudo pacman -S xorg xorg-server xorg-xrandr`.
* When asked for which packages, unless you have a good reason to exclude any, just hit `Enter` to use all.
* Then you will be asked to choose which GL library to use, again, hit `Enter` unless you have a reason to not use `libglvnd`
* Then install `sudo pacman -S nvidia`
* Now it's time to install `gnome` or your desktop-environment/window-manager of choice.
    * `sudo pacman -S gnome gnome-extra`
    * Again, use the default packages by hitting `Enter` unless you have a good reason not to use some of them.
    * It will make you choose a few things:
        * Default font, just use the default `noto` for now and change later.
        * Which video decoder, most people won't need `10bit x264` so go with the defulat.
        * I don't know why it asks if you want `xdg-desktop-portal-gtk` or `*-kde` since gnome is a `gtk` based environment, go with the default of `gtk`.
* Then install `lightdm` as the display manager, or use another one if you know what you're doing and prefer something else.
    * `sudo pacman -S lightdm lightdm-gtk-greeter`
    * Enable the `lightdm` service so it starts on bootup to initialize the UI.
        * `sudo systemctl enable lightdm`
    * Then start it to see if it all works, `sudo systemctl start lightdm`
    * If it works the greeter for logging into the desktop environment should now be visible, try logging in.



Install Trizen to Get AUR Packages
----------------------------------

* One of the best things about Arch is that the *Arch User Repository* or *AUR*, is one of the best supplimentary linux package repositories I've ever used.
* To get the packages hosted there, a helper is useful to manage AUR packages in much the same way `pacman` does.
* My preferred AUR helper these days is [`trizen`][10].
* First get `git` to pull packages from git repositories.
    * `sudo pacman -S git`
* Then clone `trizen` from the AUR.
    * `git clone https://aur.archlinux.org/trizen.git`
    * `cd trizen`
    * `makepkg -si`
* The same commands can be done with `trizen` as with `pacman`, except `trizen` will only work with the AUR.


User Applications
-----------------

* Browser:
    * Chrome **Not Chromium**
        * I use Chrome, Firefox & Brave at different times.
        * If I want privacy I'll use firefox or brave.
        * When I want full features I'll use chrome
        * Chromium has a lot of useful features from Chrome incompatible due to its open source nature, *(chromecast, some applications, etc.)* so it doesn't make sense to me to use Chromium, if I want more speed or privacy I'll use the other two over chromium.
        *


Miscallaneous
-------------

* Install `linux-headers`, `sudo pacman -S linux-headers`.


Update CPU Microcode
--------------------

* CPU manufacturers release updates to their microcode for their CPUs for performance tweaks, added features, and crucially now with Spectre and Meltdown, for security improvements.
* This can be done in Arch by downloading the `intel-microcode` package.


References
----------
[02]: https://wiki.archlinux.org/index.php/General_recommendations "Arch Wiki: General Recomendations"
[03]: https://wiki.archlinux.org/index.php/Security "Arch Wiki: Security"
[04]: https://wiki.archlinux.org/index.php/Sudo "Arch Wiki: Sudo"
[05]: https://wiki.archlinux.org/index.php/Category:Desktop_environments "Arch Wiki: Category:Desktop Environments"
[10]: https://github.com/trizen/trizen "Github: trizen/trizen"

2. [Arch Wiki: General Recomendations][02]
3. [Arch Wiki: Security][03]
4. [Arch Wiki: Sudo][04]
5. [Arch Wiki: Category:Desktop Environments][05]
10. [Github: trizen/trizen][10]

