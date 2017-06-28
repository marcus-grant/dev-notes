# Security Notes
General security notes that I make along the way on securing my various personal & professional systems I'm responsible for. Also the occasional white-hat hack or exploits I hear about.


## SSH
  
- From [aMANSworld on r/linuxadmin](https://www.reddit.com/r/linuxadmin/comments/2lravs/fail2ban_does_not_detect_my_ssh_privatekey/)

> Well, first off, it's practically impossible for someone to "guess" or brute-force your key (providing you've used a sensible key-length and algorithm), so there's no real need to limit these attempts.

> Secondly, it's really not a bad idea to use rate limiting in iptables, as having the kernel filter out any attempts takes less resources than trying to initiate the ssh-connection (and how often do you need to initiate hundreds of ssh-connections over a short amount of time? - so why not rate limit?).

> Thirdly, fail2ban is not a very good choice for protecting against brute-force attacks, as it does not work with IPv6. Brute-force attacks over IPv6 are still relatively rare, but in these modern times, it will only become more common. SSHGuard could be a better choice, though it has fewer options for customization. Personally I utilize a combination of the two, having SSHGuard take care of ssh and imap/smtp, and using fail2ban to do custom blocking of other things.

[link to SSHGuard]()

- Clearly it is better to rate limit the connection when using public keys (public keys on & password access off are **NOT** optional)
- Fail2ban could be useful for other things that aren't nearly as sensitive as system access
- SSHGuard could be used to add an extra layer in an actually effective way when using said pubkeys


## VPN
- [Digital Ocean's guide](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-openvpn-server-on-ubuntu-14-04) on this is quite good
- main config should be located in `/etc/openvpn/server.con`
- there are also decent configs in `/usr/share/doc/openvpn/examples/sample-config-giles`
- only use them as templates, more work should be put into them to be truly secure
  - `dh dh1024.pem` should be at least `dh dh2048.pem`
    - doubles the RSA key length used for generating server and client keys
    - these days 1024 using diffie helman is mediocre at best
  - `push "redirect-gateway def1 bypass-dhcp"` should be uncommented
    - This is to pass client's web traffic to its destination, so it simply becomes an endpoint and entrance to/from clients
  - `push "dhcp-option DNS 208.67.222.222"` & `push "dhcp-option DNS 208.67.222.220"` should be uncommented
    - This tells the server to push traffic through the [OpenDNS]() servers for DNS requrests
    - Other DNSs can be specified
    - Should this server become my own personal DNS?
    - Should this server become a security gateway?
    - Helps prevent DNS requests to leak outside VPN connection
    - **NOTE** clients will also need DNS resolvers specified
      - why? and how does dns works in vpn systems? **TODO**
  - comment out these two lines so they look like `user nobody` & `group nogroup`
    - By default openVPN runs as root, giving full root access, bad
    - Instead give only unpriveleged user and group `nobody` & `nogroup` to openVPN
      - Good for running untrusted applications like web facing servers
      - Installed with openVPN install
  - Should a different port be used than the default of 1194?
- Next there need to be some `sysctl` changes to tell kernel to forward traffic from client devices to internet and back
  - Without this traffic will get stuck on the server
  - enter `echo 1 > /proc/sys/net/ipv4/ip_forward` to make sure it forwards these things on currently running system
  - next make it permanent by editing some configs that will make this happen on next reboot as well
  - in `/etc/sysctl.conf`
    - uncomment `net.ipv4.ip_forward`
- **UFW** needs to be configured so the firewall blocks the right stuff and permits you and only you
  - Refer to DigitalOcean's [guide](http://do.co/1k71XXr) to setup a good cloud facing firewall config
  -
