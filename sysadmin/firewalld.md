Firewalld Notes
===============

*General notes on firewalld, the firewall daemon for linux (default on Fedora).*

* **TODO:** add stuff that comes from both local server captain's logs.


Overview (for Article)
----------------------

Firewalls are the first line of defense for computer systems hooked up to the internet. As multiple high profile data leaks and attacks on services used by basically everyone, it might be worth noting what a firewall does and how to use oneby showing how [firewalld][01], a popular linux firewall tool runs. Basically a firewall determines if packets *should* be allowed to enter or leave a network based on information that can be gathered about the packets. Typically based on [IP-Addresses][02] and [TCP/IP Ports][03]. It does so using *circuit-level gateways*, *packet filtering*, *proxy servers*, & *application gateways*.

### Packet Filtering

Packet filtering involves intercepting all traffic to/from the network & evaluating it certain rules that are provided by default or by a user. Typically this involves inspecting a packet's header portion of data which will contain, *among other things*, source & destination IP addresses and source & destination ports. Ports are basically just a 16-bit number *(0-65535 in decimal)* that gets applied to a packet by programs that use the network it's on. By examining these four pieces of data on each packet and comparing them to filtering rules, a packet can either be rejected or accepted in/out of the network.

### Circuit-Level Gateway

A circuit-level gateway blocks all incoming traffic to any host but itself. Then client machines inside the network run software that allows them to establish connections to the world outside the circuit-level gateway. To the larger world it appears as if all communication from the internal network comes from the circuit-level gateway. Then higher level software can be run on this gateway that provides greater access controls to the network.

### Proxy Server

Proxy servers are very useful and versatile pieces of tech. They can boost performance, act as firewalls, and reroute traffic in many useful ways. They hide the internal addresses of the internal network so it appears it all comes from the proxy itself. So essentially internal devices tell the proxy what to do, then the proxy will reform the packets to appear as if it's coming to/from it. Basically just swapping the headers to packets being sent through it. Proxies, since they are connected to the outside world can also have additional rules that can be specified easier when they're part of a gateway to the internet like blocking certain sites. And they can be used as reverse proxies, turning requests into the internal networks, based on the parameters of the request to go to different places.

### Application Gateway

Application gateways are in a sense another kind of proxy server. They are however typically more complex in that although they can't swap the headers like a proxy, it can do other more complex things like inspect packets. Packet inspection involves actually reading the contents of a packet and filtering based on rules specified for what is allowable for a packet to enter the network with. More modern application gateways will actually use concepts like neural networks to learn what normal traffic on the network looks like and accept and reject them based on what it has learned.


Firewalld Basics
----------------

To demonstrate some of the functionality of firewalls, here is how firewalld acts as a firewall on linux systems.

### Install Firewalld

* Firewalld comes pre-installed and configured on a lot of linux distributions.
* However, if it doesn't, I'd first lookup distributions specific instructions on how to install it before proceeding.
* I primarily use Red Hat distributions: *fedora* & *CentOS*.
* Both have it pre-installed.

### Manage the Service

* Start the service: `systemctl start firewalld`
* Enable the service: `systemctl enable firewalld`
* Stop the service: `systemctl stop firewalld`
* Disable the service: `systemctl disable firewalld`
* View the status of the daemon: `systemctl status firewalld`
* View the state of the firewall: `firewall-cmd --state`
* Reload the configurations of the firewall: `firewall-cmd --reload`

Configuring the Firewall
------------------------

* Configuration files are stored in `/etc/firewalld` & `/usr/lib/firewalld`.
* All configurations are made using XML files in these locations.
* Firewalld configurations are made in two modes, *runtime* & *permanent*.
* Runtime configurations are not kept on reboot or restarting the service.
* `--permanent` flags applied to the `firewall-cmd` command makes configurations permanent.
* Commands are issued to the firewall daemon using `firewall-cmd`.
* When no `--permanent` option is given, it's assumed to be a runtime command.

Adding Predefined Services to Firewall
--------------------------------------

* Services are predefined rules that tells the firewall to not reject packets following these rules.
* Typically this involves just basic packet filtering of ports and addresses.
```sh
sudo firewall-cmd --zone=public --add-service=http --permanent
sudo firewall-cmd --zone=public --add-service=http
```
* This lets traffic that follows the `http` *(port 80)* service through the wall.

Firewall Zones
--------------

* An abstraction to applying rules to different network interfaces.
* Zones are named rulesets that can be specified to different ports, or changed on the fly.
* Typically `firewalld` comes with predefined zones like `home`, `trusted`, `public`, etc.
* `public` is going to be very restrictive since it's intended for public network.
* If on a laptop, applying `public` as the default zone to wifi interfaces should be quite advisible when not at home.
* Find out the current default zone: `firewall-cmd --get-default-zone`
* To change the default zone: `firewall-cmd --set-default-zone=home`
* To see zones in use by machine: `firewall-cmd --get-active-zones`
    * typical output.
```
public
  interfaces: eth0
```
    * This shows the network interface under the zone it's configured to.
* To get all configurations for a zone: `firewall-cmd --zone=public`.
    * This shows a list of interfaces and the services, ports, forwarding, etc. rules that are in place.
* To get all configurations for all zones: `firewall-cmd --list-all-zones`

Adding Services
---------------

* Firewalld considers any predefined or user defined rules for the firewall as a *service*.
* It's possible to add your own rules to a service, then add that service to any zone the firewall is using right now, or could use later.
* To see all available services to use: `firewall-cmd --get-services`.
* To add a service: `sudo firewall-cmd --zone=public --add-services=ssh --permanent`
* To remove a service: `sudo firewall-cmd --zone=public --remove-service=ssh --permanent`
* These `--permanent` rules however don't apply until either the service is restart through `systemctl`, or the firewall itself is reloaded `--reloaded`.


Specifying Custom Ports
-----------------------
* Sometimes the predefined services don't specify all kinds of traffic.
* And sometimes creating a custom service is overkill.
* In this case just specify a port to allow.
* To add a custom port: `sudo firewall-cmd --zone=public --add-port=1234/tcp --permanent`
* To remove a port: `sudo firewall-cmd --zone=public --remove-port=1234/tcp --permanent`

### Port Forwarding

* Sometimes forwarding a port is desired, especially if this device is a gateway to the internet, or a very large network.
* This is done by masquerading a port as something else and then sending the port to another destination.
* Here is an example of forwarding the `http` service to port `1234` on the same host on the address `123.45.67.89`.

1. Activate the masquerade: `firewall-cmd --zone=public --add-masquerade`
2. Add the forwarding rule:
```sh
firewall-cmd --zone=public --add-forward-port=port=80:proto=tcp:toport=1234:toaddr=123.45.67.89
```
* This might be done for example if web traffic is blocked, but other ports should be permitted.
* To remove the rule: `firewall-cmd --zone=public --remove-masquerade`


Add Custom Service
------------------
* Start by specifying a new service with `--new-service` & `--permanent` option.
    * `firewall-cmd --permanent --new-service=myservice`
* Configure the service with all the relevant details.
```sh
firewall-cmd --permanent --service=service --set-description=description
firewall-cmd --permanent --service=service --set-short=description
firewall-cmd --permanent --service=service --add-port=portid[-portid]/protocol
firewall-cmd --permanent --service=service --add-protocol=protocol
firewall-cmd --permanent --service=service --add-source-port=portid[-portid]/protocol
firewall-cmd --permanent --service=service --add-module=module
firewall-cmd --permanent --service=service --set-destination=ipv:address[/mask]
```
* Alternatively you can create an XML file of this form.
```xml
<?xml version="1.0" encoding="utf-8"?>   
<service>
    <short>A brief title</short>
    <description>A long description, maybe mention the ports in use and for what application. (1235/udp)</description>
    <port protocol="udp" port="1235"/>
</service>
```
* Then just add the file: `firewall-cmd --permanent --new-service-from-file=myservice.xml`
* Add the rule: `firewall-cmd --permanent --add-service=myservice --zone=home`
* Then reload: `firewall-cmd --reload`



References
----------

1. [Firewalld Documentation: Home][01]
2. [Wikipedia: IP Address][02]
3. [Wikipedia: TCP/IP][03]

[01]: http://www.firewalld.org/documentation/ "Firewalld Documentation: Home"
[02]: https://en.wikipedia.org/wiki/IP_address "Wikipedia: IP Address"
[03]: https://en.wikipedia.org/wiki/Internet_protocol_suite "Wikipedia: TCP/IP"
