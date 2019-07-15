Simple Traefik Tutorial
=======================

Overview
--------

*** TODO Fill in these summary points and add to them an overview of what each does***
**Consider splitting this up into the install docker phase and add a simple container, then installing traefik with a summary of how to register new frontends with new backends, then portainer as its own thing, probably after showing how to setup a much simpler container**

- Install Docker using the OS's package manager, in this case `apt`.
- How to install `htpasswd` and use it to create hashed passwords needed for logging into Traefik & Portainer safetly.
- Configure traefik's routing, docker socket, and TLS certification gateway using `traefik.toml`.
- Adding a virtual network to docker
- Creating bash scripts that hold all the docker configuration options for each container so they're persisted, more easily edited, and accessible in the future.
- Creating such a script to create and run the Traefik container with an `acme.json` file to store all the Let's Encrypt ACME protocol information to encrypt all traffic.
- How to use docker labels to inform Traefik of new containers and how to map them to subdomains.
- How to create docker volumes to deal with more demanding storage I/O like what's required by Portainer to function.
- Keeping containers running indefinitely even through crashes using `--restart`.
- Synthesizing all of this to start the Portainer container which needs all of these aspects toghether.


Install Docker
--------------

This is being done on a Ubuntu server, but should just as well work on a Debian one as well since they both use `apt` as a package manager and often use the same package names, in this case both will install Docker in the same ways. Because docker is such a crucial piece of software, we'll do the long way that ensures we verify the checksums of the download and use HTTPS to download it. This will also ensure we're using more recent versions of Docker because Docker's official repository will be more up to date and tested more frequently.

First update the existing list of packages:

```sh
sudo apt update
```

Then install the prerequisites for `apt` to use HTTPS:

```sh
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

Add the GPG key for the official Docker repository which allows automatic checking of checksums during the install process ensuring your copy is legitimate.

```sh
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

*For Debian*:

```sh
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
```

Add the Docker repository to `apt`'s list of software sources, note this one and the next one will be different between Ubuntu and Debian so look up the specifics for Debian if that's what you're using. For Ubuntu, use these below:

```sh
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
```

*For Debian*:

```sh
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
```

Update the repository list again:

```sh
sudo apt update
```

Make sure you're using the official Docker repository to install `docker-ce` instead of the Ubuntu or Debian default version:

```sh
apt-cache policy docker-ce
```

You should see something like this below, note that the url shown is from Docker and not Ubuntu, Debian or Canononical (Ubuntu's Foundation).

```sh
docker-ce:
  Installed: (none)
  Candidate: 18.03.1~ce~3-0~ubuntu
  Version table:
     18.03.1~ce~3-0~ubuntu 500
        500 https://download.docker.com/linux/ubuntu bionic/stable amd64 Packages
```

Now Install the thing, which now should use the official Docker repository:

```sh
sudo apt install docker-ce
```

With Docker installed let's make sure that docker is running as a daemon, which will be important to ensure that docker is always running, and that even if the server restarts it will start up with the OS.

```sh
sudo systemctl status docker
```

And you should see something like this, not the `Active: active (running)` line which says that it is active and running and will remain so on a restart.

```sh
docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2018-07-05 15:08:39 UTC; 2min 55s ago
     Docs: https://docs.docker.com
 Main PID: 10096 (dockerd)
    Tasks: 16
   CGroup: /system.slice/docker.service
           ├─10096 /usr/bin/dockerd -H fd://
           └─10113 docker-containerd --config /var/run/docker/containerd/containerd.toml
```



Configure & Run the Thing
-------------------------

The Traefik project, being a docker-centric project, of course has its own [official docker image][01]. Let's set it up. First, some configurations. Let's set it up to display its dashboard web server which is included in the image, it's basic http/https endpoints and an encrypted administrator password.

To easily create a hashed password, Traefik knows to look for `htpasswd` formatted strings to assign username and passwords to people logging into the dashboard. Let's install it:

```sh
sudo apt-get install apache2-utils
```

Come up with a strong password that you will remember, or store in a password manager, this guide will use `strong_password`. Then think of a username to be the administrator account, in this case `admin` and use `htpasswd` to create a hashed user and password string:

```sh
htpasswd -nb admin strong_password
```

Which should look like this in the terminal:

```sh:output
admin:$apr1$czLMwYJR$NTrBSuI7qCqm3VismVqDC/
```

Now you have a hashed password, just please don't use `strong_password` as your password, it's not actually strong, and it's listed here. Whatever password you chose should show up as a garbled mess like it does here since it has been cryptographically hashed. Keep this output in mind and its associated username and password, because it's going to be used to login to Traefik's dashboard later on.

To configure Traefik, [TOML][02] files are used, they're a spiritual successor to the INI format, but standardized and some new hotness thrown in. Traefik expects a `traefik.toml` file within its container to know how to run itself. It has modules like `api` to setup networked endpoints, managing `docker`, and `acme` to manage TLS certificates using Let's Encrypt.

Using your favorite terminal editor, mine is `vim` but use what you like, and add in these lines:

```sh
vim ~/traefik.toml
```


```toml
defaultEntryPoints = ["http", "https"]

[entryPoints]
  [entryPoints.dashboard]
    address = ":8080"
    [entryPoints.dashboard.auth]
      [entryPoints.dashboard.auth.basic]
        users = ["admin:$apr1$czLMwYJR$NTrBSuI7qCqm3VismVqDC/"]
  [entryPoints.http]
    address = ":80"
      [entryPoints.http.redirect]
        entryPoint = "https"
  [entryPoints.https]
    address = ":443"
      [entryPoints.https.tls]

[api]
entrypoint="dashboard"

[acme]
email = "your_email@your_domain"
storage = "acme.json"
entryPoint = "https"
onHostRule = true
  [acme.httpChallenge]
  entryPoint = "http"

[docker]
domain = "your_domain"
watch = true
network = "web"
```

Don't worry, this will be explained now. Don't feel the need to go through all of it, focus on the stuff that should be custom to you. Go through all of them though if you want to understand more about Traefik.

`defaultEntryPoints` defines how all traffic enters `traefik.` `http` and `https` are the names given to the two valid entrypoints. This is being setup to only allow traffic through the standard ports of those two protocols. It's possible to use others, but it's easier to secure a system when the outside world can only access a few ports. `api` is a module within Traefik that maps incoming URI subdomains and suffixes to services behind Traefik. `entrypoint=dashboard` simply names the trefik dashboard as `dashboard`.

`[entrypoints]` defines a collection of entrypoints for Traefik to pay attention to, this is a recognized keyword and in TOML, square bracketted and indented entries defines a dictionary entry. `[entrypoints.dashboard]` as a dictionary entry for `entrypoints` configures the `api` module and how traffic reaches it. The dashboard is built into Traefik's container. `address = ":8080"` is where the docker container will listen for traffic into the Traefik dashboard, this will be set up with docker later.

`[entryPoints.dashboard.auth]` and its nested properties including the hashed password created earlier are just configurations that tell Traefik the authentication details it needs to have password logins for the dashboard. `[entryPoints.http]` & `[entryPoints.https]` define configurations for the how we handle the only two means for accessing services from the outside internet, on ports `80` and `443`. `http` is standard on port `80` and when you type `http://` into your browser, you're merely telling your browser to use port 80 to reach the web address you're pointing it to. `https` is standard on port `443` and unlike `http` it isn't secure, all traffic in `https` is encrypted using TLS/SSL so someone snooping on your connection can't see the credit card numbers you're sending through the internet. Because `https` is the standard way to send secure information in the internet, it would be nice if all our services used it. `[entryPoints.http.redirect]` tells Traefik that if a service is accessed through `http` it's going to be redirected, in this case to `https`.

`[acme]` defines the Let's Encrypt protocol [ACME][03] which is how it manages TLS/SSL certificates that secure entry into the services running behind Traefik. `email` is required because certificates need an email address to identify who owns a certificate, and to verify its ownership. `storage = "acme.json"` specifies where Traefik will look for the acme file which stores information recieved from the certificate authority within Let's encrypt. Because the goal of `acme` is to encrypt, make sure its pointed towards the ``entryPoint ```https`. `onHostRule = true` dictates that Traefik should get certificates for services as soon as they're created. `[acme.httpChallenge]` is a section where Traefik is told certificates should be generated as part of a challenge on the `http` entrypoint.

`[docker]` is a Traefik module that deals with proxying traffic in and out of the docker daemon that runs on the server. `watch` specifies that Traefik should pay attention to new or updated docker containers and update its routing based on it. `network = "web"` specifies that an internal docker network, named `web` has been created and that's where Traefik should route traffic to. `domain` specifies the domain of the server and it's needed to know how to split out subdomains like `traefik.your_domain` to point to the Traefik dashboard service or any other subdomains we'll define soon.

Phew, that was a lot to take in, but assuming this was typed in correctly, this is all that should be needed to get it running in the next section.


Run the Traefik Container
-------------------------

OK, enough talk, let's do stuff. Get docker to create its virtual network defined earlier as `web`:

```sh
docker network create web
```

Create an empty file for the `acme.json` file defined earlier to hold our Let's Encrypt info in:

```sh
touch ~/acme.json
```

Traefik only reads this file if the root user inside the Traefik container has read and write access to it, and we generally want it to be secure, so change its permissions to:

```sh
chmod 600 acme.json
```

Then before actually running Traefik, we'll create a useful script to run traefik on docker with all the many parameters we need for it. Create a file named whatever you want, in my case `run_traefik.sh` which will be a script to start traefik:

`~/run_traefik.sh`
```sh
sudo docker run -d \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $PWD/traefik.toml:/traefik.toml \
  -v $PWD/acme.json:/acme.json \
  -p 80:80 \
  -p 443:443 \
  -l traefik.frontend.rule=Host:traefik.your_domain \
  -l traefik.port=8080 \
  --network web \
  --name traefik \
  traefik:1.7.2-alpine
```

Then give this file execution permissions with `chmod +x ~/run_traefik.sh` so you can actually run it. Then run it with `~/run_traefik.sh` . 

And if you go into your browser and type `https://traefik.your_domain` you should be seeing a login screen for traefik where you can enter your administrator user name and password, which hopefully you haven't forgetten yet. Which should look a little something like this:

![Trefik Dashboard on First Run][p1]

You'll notice two sections, `Frontends` & `Backends`. I filtered out everything on my docker server because there's tons of things running there, you should only see the front and backends for traefik if you're following along. Frontends are what's visible to the outside world, like `traefik.your_domain`. Traefik then maps this front end address to the internal `web` docker network defined previously. These show up to traefik through docker as `backends`. I'd write out the backend address, but it's actually a completely seperate internal network that will differ between servers, so in effect traefik now is a router performing NAT, *Network Address Traversal*, which is exactly what your home router is doing when you type in a really awesome website's web address, like [patternbuffer.io](https://patternbuffer.io).

A DNS server translates that address into an IP address, your browser requests a page from that address, that page is sent to your home address which is embedded in that request, your router translates your home address to your computer's address inside your network, and you get the page requested. On Traefik on a server on `your_domain`, when you type `traefik.your_domain`, you get the internet address again from a DNS server, if your host provider provides DNS for your servers it goes through there, then the request comes into Traefik, it recognizes the subdomain `traefik`, it translates it to the dashboard container's internal virtual network address, and it returns to you the dashboard. To learn more about it, the Traefik team has a very nice and well documented [page][05] explaining the basics.

In the interface of the dashboards you'll see how the front ends are mapped to the backends visible to traefik. I've blurred out my domains which will be different for you anyways, because the internet.

With that out of the way, I'll show the script created to run traefik on docker with some explanations:

```sh
sudo docker run -d \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $PWD/traefik.toml:/traefik.toml \
  -v $PWD/acme.json:/acme.json \
  -p 80:80 \
  -p 443:443 \
  -l traefik.frontend.rule=Host:traefik.your_domain \
  -l traefik.port=8080 \
  --network web \
  --name traefik \
  traefik:1.7.2-alpine
```

First, note the `\` backslashes, in most linux terminals this corresponds to a linebreak, usually when you hit enter, you don't create a linebreak, you run the command. This command is so long that it's easier to read and edit if you break it into multiple lines.

The parameter `-v` specifies environment variables that we define for the container. These are used to pass information the container needs to run properly. `/var/run/docker.sock` is what's known as a socket file. This is where docker places its socket file, by defult and it's used to share information about currently running docker containers to other programs like the Traefik container, through a process known as *Inter Process Communication* or IPC for short. It's then mapped to the expected location inside the container to look for that information `/var/run/docker.sock`. The same is done for `traefik.toml` & `acme.json` because we need to share these files from the host to the Traefik container for it to work as expected, but these are just regular file input/output operations, docker by itself knows the difference.

Port mappings outside and inside a docker container are handled with the `-p` parameter. This is a simple mapping in this case because we are only listening to port `80` & `443` for `http` & `https`. Because we have no reason to change how these ports are mapped they are the same both inside and outside Traefik's container.

[Docker Labels][06] are managed with the `-l` parameter. Basically they are just key-value variables that store information, just like environment variables. There is one crucial difference however, **labels on one container can be read by others**. Traefik uses them to define things like `frontend.rule`, which tells the traefik container where to listen for your subdomain `traefik` to reach its dashboard page. It's also used to define what the docker listening address for that page is, `8080` in this case. There's more nuance than that, but let's not go down too many rabbit holes here, go to the link if you're interested in learning more.

Docker uses `--network` as a paremeter to specify which docker virtual network to use for this container, in this case `web`. It's possible to create multiple virtual networks, but unless you're managing a pretty complex server or cluster you don't have much reason to worry about multiple networks, just create one, name it, and put all containers on it. And `--name` is similarly simple, it's just the name applied to this container to help identify it when you start having more than one container running, which you will shortly.

Then lastly, the parameter which doesn't use a `-` parameter flag is the name of the container on docker hub to use, in this case version 1.7.2 of the official alpine container of Traefik. On the first run, docker will download this container, build it using the parameters defined in this script, and run it.

There is one potential caveat to deal with here. If you're like me and hosting on Digital Ocean and you use A records on your server's domain, you may need to add A records for each of your subdomains that traefik uses, otherwise your host provider's name servers won't know how to respond to the subdomain. I use them because I don't just host one server on digital ocean and I need to be able to reach some of their other services through the same domain. If this is anything like your situation, be it Digital Ocean, or AWS, or what have you, keep this in mind if something isn't working.


Adding New Containers & Registering them with Traefik
-----------------------------------------------------

This container isn't terribly interesting by itself, so far it only routes a subdomain of its server's domain. Traefik is only interesting once you start managing multiple docker containers. Other than Traefik, I always recommend [Portainer][07] as one of the first containers for people to use. And that is because for newcommers to Docker it's nice to have a graphical web app to manage docker instead of just the command line interface. Though it must be said, docker has one of the nicer command line interfaces I've encountered. I still use Portainer for quickly glancing at the status of my containers because web pages are just easier to access with a phone than a command line, but it can do a lot more than that.

With Traefik now running, it's time to go over another feature of Docker, [Volumes][08]. Without going too in depth, since the Docker team does in that link, before we used bind paths to link some of the host's files to give Traefik persistent storage, now we use are going to use Volumes. Volumes are basically virtual filesystems stored somewhere on the host or on a networked server. Portainer requires this for some unknown reason in their container, maybe because volumes can more easily be pre-populated with data, but there's several other useful features that they provide over bind mounts should you be interested in investigating further. Now let's create a volume for portainer, and look at it to verify it worked:

```sh
sudo docker volume create portainer_data
sudo docker volume inspect portainer_data
```

Which should show something like this output:

```sh
[
    {
        "CreatedAt": "2019-05-27T20:56:29Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/portainer_data/_data",
        "Name": "portainer_data",
        "Options": {},
        "Scope": "local"
    }
]
```
Now docker has created a volume called `portainer_data`. Next setup a script to run portainer, just like we did to get Traefik running. Again, it's useful to keep these scripts lying around because it makes it **so much easier** to change configurations and you don't need to remember all the parameters each container is running with.

There's a new caveat to Portainer in recent versions. Without a specified admin password, the container will shut itself down for security reasons. This prevents starting portainer and accidentally forgetting to set a password, which could be very bad indeed. Using the same `htpasswd` command as before with an extra command thrown in, we can create a password for portainer.

```sh
htpasswd -nb -B admin strong_password | cut -d ":" -f 2
```
This will print out a hashed password just like before, but without the username and `:` colon that separated them before. The username isn't necessary in portainer, by default it creates a user `admin` that has admin priveleges and it will take this password hash to authenticate it. Create a `run_portainer.sh` or similar script with your terminal editor like so:

`run_portainer.sh`
```sh
#!/bin/bash
sudo docker run -d \
    --network web \
    --name portainer \
    --restart always \
    -p 9000:9000 \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v portainer_data:/data \
    -l traefik.frontend.rule=Host:portainer.your_domain \
    -l traefik.backend=portainer \
    -l traefik.docker.network=web \
    -l traefik.port=9000 \
    portainer/portainer \
    --admin-password='YOUR_HASHED_PASSWORD_INSIDE_THE_QUOTES'
```

This should look familiar to running Traefik as a container. Detached, or `-d` as an option is when you run a container without printint the output logs of the container. Generally when manually running containers you'll want to use this command because otherwise your shell will be stuck printint the output of that container and accept your inputs in the virtual machine. Then pressing `Ctrl+c` or `Ctrl+d` will close the container.

For every container that you wish to use with traefik, you should be using the same Docker virtual networks you defined in `traefik.toml`, in this case `web` as specified after `--network`.

The `--name` option again specifies what the container is known as to docker, which is always useful and should be used on basically any container added. `--restart` however should only be used on containers that you won't often be changing and need to be kept up indefinately and both Traefik and Portainer fit this bill so we include it. In later articles we'll go over how to automatically update containers and still keep them running by restarting automatically.

Again `-p` is used to map ports, portainer by default uses port `9000` so we'll stick with that on both sides. This is also another usage of the docker socket file like Traefik does. This isn't a common thing for Docker containers, but because this container, just like Traefik, is concerned with observing and changing the way the Docker hosts and runs its containers, it's used here as well. This **is not necessary for most other containers**, so don't think it's a requirement for every container you run, most actually shouldn't know this information.

Docker labels, or the `-l` option are used here, and on any future container that needs to be routed by traefik. The usual labels are applied, `traefik.frontend.rule`, `traefik.backend`, `traefik`, `traefik.docker.network`, and `traefik.port` together give traefik all the information it needs to properly route `portainer.your_domain` to the `portainer` backend on port `9000`.

We also point the portainer volume on the host `portainer_data` to an expected directory inside the container, `/data`. Then we specify the hashed `admin-password` so that portainer starts with a valid password and doesn't terminate itself for safety concerns.

To verify that the container is running, this command is very useful, and should result in something like this:

```sh
sudo docker container ls
```
```sh
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                                      NAMES
6f905bceea05        portainer/portainer    "/portainer"             31 seconds ago      Up 30 seconds       0.0.0.0:9000->9000/tcp                     portainer
757777885d13        traefik:1.7.2-alpine   "/entrypoint.sh trae…"   4 hours ago         Up 4 hours          0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp   traefik
```

First of all, the fact that the `Image` and `Names` columns show both `portainer` and our previously created Traefik container running, which means they're running. Correctly is another story, but you can at least verify ports and age of the containers. This also highlights the importance of the `--name` option, because without it each container would be given a randomly generated adjective-proper-name combination which doesn't necessarily describe the container, though the names are funny sometimes.

Now finally, go to `portainer.your_domain` and log in using user `admin` and your password you hashed and you will see this page:

![Initial Screen of Portainer After Successfully Logging In][p2]

It's important to select the right mode for portainer here, because it will be saved and used in the future, requiring you to delete the `portainer_data` volume and start over. Make sure you chose the `Local` option listed in the screenshot above if you're installing portainer to manage the local docker server. Otherwise choose remote. If you're reading this guide, I would guess that you're much more likely to want the `Local` option. If all is well, you'll go into the home screen of portainer and you'll see this status card for the local Docker server indicating it's running with two containers.

![Portainer Local Docker Status Card is Running][p3]


Summary
-------

So this ended up being a **much** longer article than I thought. There's so many small details involved in setting up not only docker, not only traefik as a container, and also portainer as a container. But I think it's hard to explain how to setup Traefik without also setting up docker, or setting up docker without setting up a container, so I think it might be justified to go this long. And because it went this long I'll close by summarizing what was done and what should be done in the future for new Docker containers and routing them with Traefik. We learned here how to:

- Install Docker using the OS's package manager, in this case `apt`.
- How to install `htpasswd` and use it to create hashed passwords needed for logging into Traefik & Portainer safetly.
- Configure traefik's routing, docker socket, and TLS certification gateway using `traefik.toml`.
- Adding a virtual network to docker
- Creating bash scripts that hold all the docker configuration options for each container so they're persisted, more easily edited, and accessible in the future.
- Creating such a script to create and run the Traefik container with an `acme.json` file to store all the Let's Encrypt ACME protocol information to encrypt all traffic.
- How to use docker labels to inform Traefik of new containers and how to map them to subdomains.
- How to create docker volumes to deal with more demanding storage I/O like what's required by Portainer to function.
- Keeping containers running indefinitely even through crashes using `--restart`.
- Synthesizing all of this to start the Portainer container which needs all of these aspects toghether.




References
----------

1. [Official Traefik Docker Image][01]
2. [Github: TOML][02]
3. [Github: ACME][03]
4. [EFF: Certbot/Lets Encrypt][04]
5. [Traefik Documentation: Basics][05]
6. [Docker Documentation: Object Labels][06]
7. [Portainer Official Page][07]
8. [Docker Documentation: Volumes][08]

[01]: https://hub.docker.com/_/traefik "Official Traefik Docker Image"
[02]: https://github.com/toml-lang/toml "Github: TOML"
[03]: https://github.com/ietf-wg-acme/acme/ "Github: ACME"
[04]: https://certbot.eff.org/ "EFF: Certbot/Lets Encrypt"
[05]: https://docs.traefik.io/basics/ "Traefik Documentation: Basics"
[06]: https://docs.docker.com/config/labels-custom-metadata/ "Docker Documentation: Object Labels"
[07]: https://www.portainer.io/ "Portainer Official Page"
[08]: https://docs.docker.com/storage/volumes/ "Docker Documentation: Volumes"

[p1]: ./pics/traefik-first-run-with-filter1.png "Picture of the first run of Traefik dashboard"
[p2]: ./pics/portainer-first-run.png "Initial Screen of Portainer After Successfully Logging In"
[p3]: ./pics/portainer-local-running.png "Portainer Local Docker Status Card is Running"
