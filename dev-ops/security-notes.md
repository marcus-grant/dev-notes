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

- [Digital Ocean's guide]() on this is quite good

### Install Packages
- Install packages `openvpn` & `easy-rsa`

### Setup CA Directory
- Use easy-rsa's `make-cadir ~/openvpn-ca`
- Go to that folder

### Configure CA Variables
- edit the file `~/openvpn-ca/vars`
  - edit the export variables as below:
```
export KEY_COUNTRY="US"
export KEY_PROVINCE="NY"
export KEY_CITY="NYC"
export KEY_ORG="Pattern-Buffer"
export KEY_EMAIL="marcus.grant@patternbuffer.io"
export KEY_OU="PatternBuffer"

# X509 Subject Field
export KEY_NAME="server"                                                                                                         ```

### Build Certificate Authority
- in `~/openvpn-ca`...
- `source vars`
  - should see: something about ./clean-all

  

## References
[easy-rsa-script]: https://github.com/OpenVPN/easy-rsa "Github for OpenVPN's easy-rsa scripts and software"
