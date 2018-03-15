# Using Google Cloud Shell

## The Google Cloud Shell
* Inside the cloud platform dashboard, simply click the terminal icon on the right side of the top bar.
* Can't copy the link because it's unique to me and it may show off my personal details
* can be accessed through a local terminal by using Google's [`Cloud SDK`][02]
* When downloaded, just extract it somewhere temporary and run the `install` script, be it the shell file or the bat file depending on OS
* Then running `./google-cloud-sdk/bin/gcloud init` will initialize the local install including authenticating everything
* When inside the cloud shell, typical linux environment stuff is already there
    * Stuff like bashrcs , vim, emacs, etc.


## The "gcloud" Command Line Tool

* Once inside, the main tool to check out is the [`gcloud`][01] tool.
* `gcloud compute` - commands related to compute engine
* `gcloud compute instances` - commands related to Compute Engine instances in general availability
* `gcloud beta compute` - commands related to Compute Engine in Beta
* `gcloud alpha app` - commands related to managing app Engine deplayments in Alpha

### Use GCloud Tool to Set Compute Region

* Easy enough, just list server zones:
    * `gcloud compute zones list`
* Then pick one and set it:
    * `gcloud config set compute/zone europe-west1-d`

### Update Go

* Go is a highly supported language with GCP
* **Note**, you old bashrc `GOPATH` is defined incorrectly
* It should be that `GOBIN=$GOPATH/bin`

## Back to Udacity...


* The Udacity instructions specify their own idea of how the `GOPATH` should be managed, for me to do this requires changing how all my systems do this through my shared bashrc
	* **FUCK THAT**
* Instead, get the `go-buffer` repo
* Clone it into `~/bin/`, then rename to `~/bin/go`
* Then get the code specific to the course:
`git clone https://github.com/udacity/ud615 ~/bin/go/src/github.com/udacity/ud615`
* Then change into that folder and continue the lesson
* Make a new bin folder for the `.../app` section: `mkdir bin`
* Build to the monolith go program `go build -o ./bin/monolith ./monolith`
    * If that doens't work make sure go pkgs are up to date
        * `go get -u`
* Run the server: `sudo ./bin/monolith -http :10080`
* Test with a curl:
`curl http://127.0.0.1:10080
curl http://127.0.0.1:10080/secure`

* On another shell, authenticate that the password is `password`
* That response is a *JWT* access token, for debugging purposes it's worth saving that token:
    * `TOKEN=$(curl http://127.0.0.1:10080/login -u user | jq -r '.token')`
    * Now access with the token:
    * `curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:10080/secure`


## 12 Factor Apps

* Best-practives for building deployable Saas apps
    * Portable
    * *Continually Deployable*
    * Scalable

### The 12 Factors

* A common methodology for buidling these scalable containerised apps is to use the [The Twelve Factors][03].
1. Codebase - One codebase tracked in revision control, many deploys
2. Dependencies - explicitly declare and isolate dependencies
3. Config - store config in the environment
4. Backing services - treat backing services as attached resources
5. Build, release, run - strictly separate build and run stages
6. Processes - execute the app as one or more stateless processes
7. Port Binding - export services via port binding
8. Concurrency - scale out via the process model
9. Disposability - maximize robustness with fast startup and graceful shutdown
10. dev/prod parity - keep development, staging and production as similar as possible
11. Logs - treat logs as event streams
12. Admin Processes - run admin/mgmt tasks as one-off processes

## JSON Web Tokens (JWT)

* Compact self contained method for transferring secure data as JSON
* Used for authentication and secure data transfer
* They are basically just base64 encoded encryptions of whatever json data was given


## Building the Containers with Docker

### Creating a Google Cloud Platform Instance

* Set the compute/zone
```
gcloud compute zones list
gcloud config set compute/zone <zone>
```
* Cloud shell - log into the VM instance
```
gcloud compute instances create ubuntu \
--image-project ubuntu-os-cloud \
--image ubuntu-1604-xenial-v20160420c
```
* Log into instance 
```
gcloud compute ssh ubuntu
```
* VM instance - update packages and install nginx
```
sudo apt-get update
sudo apt-get install nginx
nginx -v
```
* Check that it's running
```
sudo systemctl status nginx
curl http://127.0.0.1
```

## Containers

Technology that makes it easy to run and distribute applications accross different operating environments

* Independent packages
* namespace isolation
* Because docker is an isolated environment, nginx could for example just keep routing from the default port 80 within the container and no init scripts would be needed

### Install Docker
```
sudo apt-get install docker.io
```
### Check Docker Images
```
sudo docker images
```

### Pull Nginx Image
```
sudo docker pull nginx:1.10.0
sudo docker images
```

### Verify the versions match
```
sudo dpkg -l | grep nginx
sudo apt-get install nginx
```

### Run First Instance
```
sudo docker run -d nginx:1.10.0
```

### Check if it's up
```
sudo docker ps
```

### Run a different version of nginx
```
sudo docker run -d nginx:1.9.3
```

### Run another version of nginx
```
sudo docker run -d nginx:1.10.0
```

### Check how many instances are running
```
sudo docker ps
sudo ps aux | grep nginx
```

### What's with the container names?

* If you don't specify a name, docker gives a container a random name, such as *"stoic_williams", "sharp_bartik", "awesome_murdock", or "evil_hawking"* rest in peace please.

* These are generated from a list of adjectives and names of famous scientists and hackers. The combination of the names and adjectives is random, except for one case. Want to see what exception is check out the docker [source code][04]

### List all running container processes
```
sudo docker ps
```
* For use in shell scripts you might want to just get a list of container IDs (-a stands for all instances, not just running, and -q is for "quiet" - show just the numberic IDs)
```
sudo docker ps -aq
```
### Inspect the container
You can use either the `CONTAINER ID` or `NAMES` field, for example a `sudo docker ps` output like this:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
f86cf066c304        nginx:1.10.0        "nginx -g 'daemon off"   8 minutes ago       Up 8 minutes        80/tcp, 443/tcp     sharp_bartik
```

* You can use either of the following commands:
```
sudo docker inspect f86cf066c304
# or
sudo docker inspect sharp_bartik
```

### Connect to the nginx using the internal IP

Get the internal IP address either copying form the full inspect file or by assigning it to a shell variable:
```
CN="sharp_bartik"
CIP=$(sudo docker inspect --format '(( .NetworkSettings.IPAddress )
curl http://$CIP
```
* You can also get all instance IDs and their corresponding IP addesses by doing this

### Stop an instance
```
sudo docker stop <cid>
# or
sudo docker stop $(sudo docker ps -aq)
```

### Remove the docker containers from the system
```
sudo docker rm <cid>
# or
sudo docker rm $(sudo docker ps -aq)
```

## Dockerfile

* Text docs that contain all the steps to build an image from a command line
* Always named `Dockerfile`
* Each line in a file starts with a command, in ALL CAPS
* Comments can also be made using `#`
* Alpine is a good default linux image for its minimal size

```
FROM alpine:3.1
MAINTAINER Kelsey Hightower <kelsey.hightower@gmail.com>
ADD hello /usr/bin/hello
ENTRYPOINT ["hello"]
```

* `FROM` defines what the base image should be, Alpine is nice because it's a mere **5MB**
    * **always** first
    * **alpine** is usually the default image
    * for every same image on a machine, it will only get used once no matter how many instances are used
* `MAINTAINER` just some metadata about who is mainting the container
* `ADD` takes a file or directory and puts them in the specified locatoin within the container image
* `ENTRYPOINT` this is the entrypoint executable for the container

## Creating an Image

* Docker can be used as a build environment as well
* For this example use go in the new instance:
```
wget https://storage.googleapis.com/golang/go1.6.2.linux-amd64.tar.gz
rm -rf /usr/local/bin/go
sudo tar -C /usr/local -xzf go1.6.2.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
export GOPATH=~/go
```

### Get the app code

```
mkdir -p $GOPATH/src/github.com/udacity
cd $GOPATH/src/github.com/udacity
git clone https://github.com/udacity/ud615.git
```

### Build a static binary of the monolith app
```
cd ud615/app/monolith
go get -u
go build --tags netgo --ldflags '-extldflags "-lm -lstdc++ -static"'

```
* Because alpine has a different implementation of `libc` it's **really** important to be a static binary
* That's why there's that ugly build command, it's because it's very sepcific to that specific dependency of alpine
* And this is a nice thing about go, it can be be compiled into a completely self contained binary

### Create a container for the app
* First look at the `DockerFile`
```
cat Dockerfile
```

### Build the app container
```
sudo docker build -t  monolith:1.0.0
```

### Run the monolith container and get it's IP
```
sudo docker run-d monolith:1.0.0
sudo docker inspect <container name or cid>
```

### Test the container
```
curl <the container IP>
# or
curl $CIP
```

## Images

### See all images

```
sudo docker images
```

### Docker tag command help
```
docker tag -h
```

### Add your own tag
```
sudo docker tag monolith:1.0.0 <your username>/monolith:1.0.0
```

### Create account on Dockerhub
* Go to [dockerhub](https://hub.docker.com/register/)

### Login and use the docker push command
```
sudo docker login
sudo docker push udacity/example-monolith:1.0.0
```



## References
1. [gcloud - Google Cloud Shell Command Line Tool][01]
2. [Google Cloud SDK Downloads][02]
3. [The Twelve Factors][03]
4. [Source code to docker names generator][04]

[01]: https://cloud.google.com/sdk/gcloud/ "gcloud - Google Cloud Shell Command Line Tool"
[02]: https://cloud.google.com/sdk/downloads#versioned "Google Cloud SDK Downloads"
[03]: https://12factor.net/ "The Twelve Factors"
[04]: https://github.com/moby/moby/blob/master/pkg/namesgenerator/names-generator.go "Source code to docker names generator"

