Setting Up a Private Docker Registry
====================================

Overview
--------

I've started putting more of my work out in the wild as a developer and inorder to smooth out the deployment workflows of those various projects, I've found that using a private docker registry is a must. I manage services with docker already on my home server, and use [traefik][10] to manage the routing, service discovery, and TLS certificate management of both my home and public services. This makes it much easier to manage services as all those factors take time when administrating and deploying them. Without getting into a complex continuous deployment pipeline which is on my list of to-dos for my personal projects, simply having an accessible and reliable docker registry is enough for one person when all you need is to run a script inside a repo to build a new container and then send it off to the registry. A simple container service [ouroboros][11] then simply watches that registry and updates those containers. This I've found to be more than sufficient for smaller projects, but sometime in the future I want to be able to build my own continuous deployment and integration piplines as well, but I've decided to wait till I've mastered kubernetes before tackling that project since a dynamic cluster would be the best computational backend for such a project.


Here you will learn how to setup and secure your own private Docker Registry.[Docker Compose][12] will be used create persisted configurations for stored images. [Traefik][11] will be used to route your domain to the registry service, and make it visible to other containers, and finally will secure it with a TLS/SSL endpoint. ***TODO*** add a reference to your traefik guide, along with guides to setup digital ocean and your affiliate link.



Initial Setup
-------------

If you haven't setup a server somewhere yet, well you may find it hard if you haven't done so already. I use [DigitalOcean][13] personally because they have some of the most quick to use tools I've encountered without the immense complexity of using someone like AWS or Azure and they beat the hell out of them in terms of pricing. If you'd like to support me on this site, setting up a docker registry using this guide and DigitalOcean through that link would be a great way to do so.

Whatever server you have setup, you'll want to follow **INSERT TRAEFIK GUIDE HERE** this guide to setup a docker, traefik and SSL certificates to before continuing. Don't worry I'll still be here when you get back.


Installing & Configuring the Docker Registry
--------------------------------------------

[Docker Compose][12] is used becuase it greatly simplifies managing multiple containers running on the same system, or if you need to keep them consistent between several systems. At its core it's just a `.yml` file that describes how to create and run each container. Then the `docker-compoes` command is used to run that container in a consistent and customized manner.

The Docker Registry, is itself a containerized service with multiple parts and configurations needed to run it, so `docker-compose` is used to manage it.

Start by creating a folder on the server where the registry will store its data, here the path is `~/docker-registry`:

```sh
mkdir ~/docker-registry && cd $_
mkdir data
```

This creates the registry's storage location and a separate folder, `data` that will hold the actual container images. Now that you're inside `~/docker-registry` use your favorite terminal editor to create its `docker-compose.yml` file:

`docker-compose.yml`
```yml
version: '3'

services:
  registry:
    image: registry:2
    ports:
    - "6666:5000"
    environment:
      REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY: /data
    volumes:
      - ./data:/data
```

This tells docker what it needs to know about this container for the private registry it is going to run.

- First the version is just our own metadata to track versioning.
- `registry:` tells it that the service is to be called `registry`
- `image:` specifies what image from the dockerhub, the biggest public docker registry should be downloaded. In this case `registry:2` or the second version of the Docker team's own docker registry container.
- `ports` specifies the port mapping, port `6666` from outside the container maps to `registry`'s default port `5000` inside it. This can be any valid port that doesn't clash with any other and later it will be managed by traefik's routing system.
- `environment:` is a key that allows for setting environment variables that docker container can use from outside the container, in this case where the registry root directory is, `/data`.
- `volumes:` this allows pointing to directories on the host server and linking them to custom directories inside the container, in this case it's the `data` directory at the same location as the `docker-compose.yml` file and its being linked to `/data` in the container root.

With that out of the way, it's time to compose the container and run it with:

```sh
docker-compose up
```

You'll see docker download the registry image and setting up the container and running it according to `docker-compose.yml`. You'll also see some warnings about certain services not being setup and of no secret being provided which has to do with authentication that will be setup later. This was mostly to test that the container was composed properly using `docker-compose.yml`, it will be automated and run by a daemon later, hit `Ctrl+c` on the keyboard before continuing.





References
----------

10. [Traefik Homepage][10] 
11. [Medium: Ouroboros][11]
12. [Docker Documentation: Compose][12]
13. [Digital Ocean Affiliate Link][13]


[10]: https://traefik.io/ "Traefik Homepage"
[11]: https://medium.com/better-programming/automatically-update-docker-containers-f2ccc79f4313 "Medium: Ouroboros"
[12]: https://docs.docker.com/compose/ "Docker Documentation: Compose"
[13]: "https://m.do.co/c/7ea7c985a5c0" "Digital Ocean Affiliate Link"

[p1]: ./pics/docker-registry-traefik1.png "Traefik dashboard showing docker registry as a backend"
