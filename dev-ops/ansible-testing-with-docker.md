Ansible Testing Using Docker
============================

Overview
--------

Although Vagrant is great for testing in general, and tends to be more stripped down than the typical virtualbox testing system, it does still have more overhead than docker. Docker *(after considering its testing/CI pitfalls)* is a nice testing environment to have because building/provisioning/teardown/destroying them is quicker and easier on the host system. But there are negatives that must be considered, especially when using a Continuous Integration pipeline like Jenkins that is itself run in a docker container or several docker containers.


A Solution
----------

Since we've been over why it can be really tricky to actually run a docker container within a docker container or **docker-in-docker**, then what is the more functional solution that still behaves much like the real thing? The answer is pointing the host's docker socket with its children docker containers.

### Using the Host's Docker Socket

**TODO** fill in details in prose and credit the author

```sh
docker run -v /var/run/docker.sock:/var/run/docker.sock # ...
```

### Docker in Docker

Since you can't nest docker instances, you have to do a work around. Instead of creating a docker container inside another, simulate the effect by passing the docker socket to the container so instead of running the docker host, inside another, it communicates with the parent docker host, while controlling it inside of it. This [issue][02] on molecule discusses it, and links to this [repository][03] that implements such a solution to test their ansible role.

To summarize, simply bound the docker socket to the child container, so that when its docker daemon starts to manage containers, it simply passes the commands to the parent and although it will behave like it's managing *child* containers, it's actually managing *sibling* containers.

`docker run -v /var/run/docker.sock:/var/run/docker.sock`

There's also a good StackOverflow [solution][04] that goes over how this docker in docker method is acheived using the above bind-mounting of `docker.sock`.

**NOTE** : I still can't seem to get this to run properly inside molecule, I've tried running the container in privileged mode and without but I can't get it to work, it might have to do with overlay2 as the driver. I also can't reliably get the molecule scripts to give helpful output or run reliably while reinstalling the python env. It's inconclusive what the problem is in molecule, it might just be a dependency problem which is always seemingly frustrating in molecule.
References
----------

1. [~jpetazzo/Using Docker-in-Docker for your CI or testing environment? Think twice][01]
2. [Github - Ansible/Molecule: Issue for nested docker containers in testing][02]
4. [StackOverflow: Docker in Docker][04]


[01]: https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/ "~jpetazzo/Using Docker-in-Docker for your CI or testing environment? Think twice"
[02]: https://github.com/ansible/molecule/issues/1322 "Github - Ansible/Molecule: Issue for nested docker containers in testing"
[03]: https://github.com/ome/ansible-role-prometheus/blob/0.2.0/molecule.yml "Github ome/ansible-role-prometheus: molecule.yml"
[04]: https://stackoverflow.com/questions/27879713/is-it-ok-to-run-docker-from-inside-docker "StackOverflow: Docker in Docker"
