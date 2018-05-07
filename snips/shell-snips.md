Shell Snippets
==============

A listing of worthy shell snippets to remember, or lookup on here.


Create a List of All Duplicate Directories
------------------------------------------


```sh
find . ! -empty -type f -exec md5sum {} + | sort | uniq -w32 -dD
```
*Taken from this [stack-overflow](https://unix.stackexchange.com/questions/277697/whats-the-quickest-way-to-find-duplicated-files).*

```sh
fd . ./ --type f --color=never --no-ignore --hidden --absolute-path --exec md5sum {} | sort | uniq -w32 -dD
```

If `[fd](https://github.com/sharkdp/fd)` is present, then this will be a good bit faster, not the least of which because it can create work pools for multiple threads to consume, so each found file can immediately be queued up to a new open thread to run the `md5sum`. Then as before, `sor | uniq` gets used to arrange the results nicely.


Print Disk (or Block Device) IDs
--------------------------------

```sh
ls -la /dev/disk/by-id/ | grep part1 | cut -d " " -f 11-20
```

This prints out a list using `/dev/by-id` and their partition listings. This can be used to fill out `/etc/fstab` in a more reliable way than their `/dev/sdX` style names.


Simulate Silent Bit Rot (Random Silent Storage Corruption)
----------------------------------------------------------

* From this [r/DataHoarder Thread](https://www.reddit.com/r/DataHoarder/comments/2c5b54/how_can_i_simulate_silent_data_corruption/)
```sh
while true; do dd if=/dev/urandom of=/dev/sdX bs=1 count=1 seek=$RANDOM; sleep 1; done;
```
