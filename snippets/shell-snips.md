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




