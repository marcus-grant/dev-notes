Shell Snippets
==============

A listing of worthy shell snippets to remember, or lookup on here.


Create a List of All Duplicate Directories
------------------------------------------

Taken from this [stack-overflow](https://unix.stackexchange.com/questions/277697/whats-the-quickest-way-to-find-duplicated-files).

```sh
find . ! -empty -type f -exec md5sum {} + | sort | uniq -w32 -dD
```

If `[fd](https://github.com/sharkdp/fd)` is present, then this will be a good bit faster

***TODO: Figure out way to make this work with `fd` instead`***


