# journalctl

## Install
- Just about any modern distro should be using `systemd` at this point which is essentially 
- ** Continue explanation of systemd using links and images **
- Because journalctl is part of `systemd` and `systemd` itself is a core part of so many installations of linux, chances are very high that the reader has it running on their linux system
- `journalctl` is just a utility that works with the daemone `journald` which centralizes the collection of log data from all active, willing and able give that information out.

## Before Using
- Make sure time and date is correct *give why later*
- `timedatectl list-timezones` to figure out which zone to use
- `sudo timedatectl set-timezone <TIMEZONE>`
- `timedatectl status`
- If these dont work and `status` shows the wrong time, give instructions on how to properly setup ntp

## Basic Usage
- `journalctl` starts the application, which polls `journald` for the lastest entries into the system log
- typically `less`, the most commonly installed stdout pager app is used to page through long outputs
  - up/down/left/right, k/j/h/l, pg_up/pg_dn/home/end will scroll through in their directions
  - `/` will activate search, a bar on the bottom will show  the search pattern typed
    - hit enter to start search with that pattern
    - use `n` to go forward in results, `N` to go backwards
- `--utc` option displays output in UTC timestamps

## Filtering by Time
- There's way too much data to process manually, so filtering becomes very important when managing a busy system
- `-b` displays only log entries of the currently booted session, nothing from the last time the system was rebooted.
 *ended here continue later*


## Filter by service or unit
- `-u nginx.service -u <NAME>.service` specifies a filter that displays only the services of name `<NAME>` in the code segment earlier
- Probably the second most useful filter to use
- Personally use to spot check security of servers to quickly see if ssh, pam_unix, or vpn, have been comprimised or if suspicious attempts are being made on the system.


## References
[DO-journalctl-tutorial]: https://www.digitalocean.com/community/tutorials/how-to-use-journalctl-to-view-and-manipulate-systemd-logs "Digital Ocean's Guide on Journalctl"
[wiki-systemd]: https://en.wikipedia.org/wiki/Systemd
[systemd-hierarchy-svg]: https://upload.wikimedia.org/wikipedia/commons/thumb/e/e7/Linux_kernel_unified_hierarchy_cgroups_and_systemd.svg/1024px-Linux_kernel_unified_hierarchy_cgroups_and_systemd.svg.png "Image showing hierarchy of systemd and its components with the curnel and front facing linux elements"
[systemd-architecture-svg]: https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Systemd_components.svg/720px-Systemd_components.svg.png "Diagram showing the architecuture of systemd"

