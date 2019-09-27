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

References
----------

1. [~jpetazzo/Using Docker-in-Docker for your CI or testing environment? Think twice][01]


[01]: https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/ "~jpetazzo/Using Docker-in-Docker for your CI or testing environment? Think twice"
