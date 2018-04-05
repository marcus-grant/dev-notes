Kubernetes & Docker Web Apps on Google Cloud
============================================

Kubernetes Helps With...
------------------------

* Docker has simple commands and configurations to *contain* applications in standardized environments, so no matter where it runs, it will run the same
* There's no `deploy` command that deploys to hosts or groups of them.
* Kubernetes is the most mature solution for this at this point
* Provides powerful abstractions decouple application operations such as deployments & scaling away from the underlying infrastructure.
* No need to work with individual hosts or virtual machines to run code.
* To Kubernetes, you just see a *sea of compute* to put containers


Kubernetes Concepts
-------------------

* Kubernetes has a client/server architecture.
* Kubernetes servers run on a *cluster* (a group of hosts to deploy on)
* Controlling the cluster involves interracting with a client
    * ... such as `kubectl`, a CLI client

### Pods

A **pod** is the basic unit that Kubernetes deals with. Basically a group of containers. If there's two or more containers that always need to work together, and be on the same machine, then they should be a **pod**.

### Node

**Nodes** are physical or vritual machines that run kubernetes where a pod can be scheduled to be run currently or at some time in the future.

### Label

**Labels** are key/value pairs that are used to identify things like cluster resources. For example, a **label** for a pod that serves production traffic could be `role=production`.

### Selector

**Selections** allow for searching and filtering resources by labels. In the previous definition, the label used to define production roles, `role=production`, could be used as a filter to only select resources with this label

### Service

A **service** is defined as a set of pods, *that are typically selected by a selector*, and a means to access them. Means of access include static IPs, DNS names, etc.



Deploy a Node App on Google Cloud Using Kubernetes
==================================================


Install Google Cloud SDK & Kubernetes Client
-----------------------------------------------

* `kubectl` is **the** CLI for running commands through a kubernetes cluster
* It can easily be installed as part of the [Google Cloud SDK][01]
    * Go to that link and figure out how to either install the SDK locally on a machine with a Linux or Linux-like terminal, or use the Google Cloud terminal tied to your account.

```
gcloud components install kubectl
```

* If on mac a lot of this is simplified by using `brew install kubectl`.
* Verify the install by running `kubectl version`


Create a CGP Project
-----------------------

* GCP resources are seperately managed within **projects**.
* Create one to practice on in the [Google Cloud Web Dashboard][02]
* If needing to refer back to the *console* for Google Cloud, here is the [console][03] to provide big-picutre manage of Google's platform.
* Set the *default project ID* for working with the CLI by running:

```
# gcloud config set project {PROJECT_ID}
# In my case my {PROJECT_ID} became   hello-kubernetes-200203 
gcloud config set project hello-kubernetes-200203 
```

![Project ID on Project Dashboard][i01]


* The `{PROJECT_ID}` should match the one listed in the dashboard card above, for whatever project is present on the current project
* This will set the current project being worked on to the one specified.


Create a Basic API Server
----------------------------

* This is a bit of an aside, but it's easier to understand if there's a really basic API server.
* In this case it's just a Node & Express API server made to return the string `Hello World!` whenever it's communicated with.
* Create a folder somewhere `./express-hello-world`
* Inside, run `npm init`
    * This will give you some basic questions about the node project.
    * Non of them are important for this, **except** the `entry point` question.
    * The entrypoint should be the default of `index.js`
* With the initialized node project ready, all there's only one dependance necessary to install, `express`
* Execute `npm install express`
* Now create a file `index.js` with the following code:

```javascript
var express = require('express');
var app = express();

app.get('/', function (req, res) {
  console.log('Request detected, sending response.');
  res.send('Hello World');
});

app.listen(3000, function () {
  console.log('Listening on port 3000...')
});
```

* Then modify the initialized `package.json` file so it at least has a script to run the demo app:

```json
{
  "name": "express-hello-world",
  "version": "1.0.0",
  "description": "A basic hello world API server using Express for testing deployments",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node index.js"
  },
  "author": "",
  "license": "MIT",
  "dependencies": {
    "express": "^4.16.3"
  }
}
```

* Now the server should be ready to run, test it by running the *now defined* npm script `start`.
* Then use any number of REST testing apps.
* I personally prefer [HTTPie][04] because it's a super easy command line utility to test APIs with.
* Using [HTTPie][04] in the command line, just run this command to test the server after having run it:

```
http localhost:3000
```

* Which should return something like this:

```
HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 11
Content-Type: text/html; charset=utf-8
Date: Wed, 04 Apr 2018 21:33:34 GMT
ETag: W/"b-Ck1VqNd45QIvq3AZd8XYQLvEhtA"
X-Powered-By: Express

Hello World
```

* If that's the case then this super basic API should be ready to made into a docker image.


Turn the Application Into A Docker Image
-------------------------------------------

* Create an empty `Dockerfile` in the app root:

```
touch Dockerfile
```

* Then edit the file to include these snippets that will be elaborated on:
* `Dockerfile`s are basically just *key/value* configuration files that tells docker about crucial information about the contianer necessary to run and maintain that container
* The key in the `Dockerfile` is just the first word of the line and is CAPITALIZED
* Then after the capitalized key, the value is just the next thing after a whitespace
* First, it's necessary to decide on what docker image to build on.
    ```dockerfile
    FROM node:7.7-alpine
    ```
    * Docker images can be thought of as shareable templates of applications that will later be containerized.
    * These are also the shared resources that docker uses to run applications.
    * In this case the `node:7.7-alpine` image is used give the dependencies required for node to run the server just created.
    * This also specifies a kind of standard environment for the container to run within, in this case `alpine`, which is basically a **highly** stripped down version of linux to only suit the needs to run the container
    * It's rare to see a linux distro these days that occupies only the *7MB* of space that alpine does
* Specify the maintainer of the image ***(optional)***

    ```dockerfile
    MAINTAINER Marcus Grant <marcusfg@gmail.com>
    ```
    * This just gives basic information about who maintains the image and in between `<...>` is an email address where that person or organization can be reached.
* Install first dependencies using `ADD` & `RUN`
    ```dockerfile
    # install node dependencies into temporary locaiton
    ADD package.json /tmp/package.json
    RUN cd /tmp && npm install
    ```
    * Essentially, `ADD` & `RUN` are just basic commands like those that would be sequentially be run in the command line.
    * Also note, that comment lines are available in the `Dockerfile` format as `#` marks.
    * Here the file `package.json`, which is required to tell node what dependencies it needs is copied onto a temporary space in the `/tmp` directory
        * More on the `/tmp` directory later
    * Then once `package.json` is copied over, `npm` can be used to install said dependencies by using the `RUN` key to tell docker what bash commands to run to prepare the dependencies

* Copy dependencies into the desired container directory location

    ```dockerfile
    # copy dependencies
    RUN mkdir -p /opt/hello-kubernetes && cp -a /tmp/node_modules /opt/hello-kubernetes
    ```

    * Here again the Docker command `RUN` is used to run a bash command that creates the directory where the complete app will reside, `/opt/hello-kubernetes`
    * Then, due to the bash `&&` operator, *which runs another command if the previous was successful*, copies all the installed dependencies from the temporary location into the final one, using `-a` to make sure the copy is using [hard links][05]
        * Essentially the hard link copy of the file is there to make the copy faster
        * Even though the original storage block started in `/tmp`, that block becomes redirected to the new file location, when the originating reference to that inode is deleted.
        * Hard links only lose reference to an inode, when all hard links pointing to the same blocks or files are deleted.

* Setup this container's working directory
    ```dockerfile
    # setup the working directory for the dockerized app
    WORKDIR /opt/hello-kubernetes
    COPY . /opt/hello-kubernetes
    ```

    * `WORKDIR`, *e.g. the root directory for the app*, sets the containers working directory
    * In this case `/opt/hello-kubernetes` is the `WORKDIR`

* Finally, run the the app

    ```dockerfile
    EXPOSE 3000
    CMD ["npm", "start"]
    ```

    * `EXPOSE` tells docker what port should be allowed to be opened to communicate with the app
        * Here it is port `3000`
    * `CMD` can only be used once per dockerfile, and it's primarly there to be the default execution of the script to launch the app.
    * In this case it uses `["npm", "start"]` to define that the `npm` command needs invoking and that `start` is the default parameter to run with.


Build the Docker Image of the App
---------------------------------

* With the docker image configured and defined within `Dockerfile`, now it's time to build it.
* Build the app image using:

```
docker build -t hello-kubernetes-image .
```

* Note, that depending on how docker is installed locally, two considerations might need to be made before being able to perform any docker operations.
    * Sometimes docker needs to be run as a daemon on the local system.
        * If that is the case, then typically `sudo systemctl start docker` will start the daemon.
        * And sometimes docker gets installed globally into *admin-level* permissions and thus, `sudo` needs to be invoked inorder to build and run docker images like the above command.
        * So if the above doesn't work, try: `sudo docker build -t hello-kubernetes-image .`
* Should this work, then the last prompt should read "`Successfully built` etc."
* Then to make sure the image is working as well as the app...
    * Run the image:
        * `docker run --name hello-kubernetes -p 3000:3000 hello-kubernetes-image`
            * `--name hello-kubernetes` defines the name to give the image.
            * `-p 3000:3000` defines how to map ports between docker and the host system, here it's from 3000 to 3000.
            * the last parameter is the name of a build or imported image to run.
    * Test the API using an API client, and see the response to `localhost:3000` like before.


Create the Kubernetes Cluster
-----------------------------

* This can also be done from the Web UI on Google, but I prefer to teach the CLI method, because it's more explicit.
* Here a cluster is to be created using the geographic zone of `us-east1-b`, never mind the `1-b`, this is just one of the possible regions to have clusters on the US eastern coast.
* Then the cluster is to be name, `hello-kubernetes-cluster`
* And the `--machine-type`, *which defines the compute resource type*, is going to be the weakest, `f1-micro`

```
# gcloud container clusters create {NAME} --zone {ZONE}
gcloud container clusters create hello-kubernetes-cluster --zone us-east1-b --machine-type f1-micro
```

* *... this can take some time ...*
* When done, this prompt should be shown:

```
Created [https://container.googleapis.com/v1/projects/hello-kubernetes-200203/zones/us-east1-b/clusters/hello-kubernetes-cluster].
To inspect the contents of your cluster, go to: https://console.cloud.google.com/kubernetes/workload_/gcloud/us-east1-b/hello-kubernetes-cluster?project=hello-kubernetes-200203
kubeconfig entry generated for hello-kubernetes-cluster.
NAME                      LOCATION    MASTER_VERSION  MASTER_IP       MACHINE_TYPE  NODE_VERSION  NUM_NODES  STATUS
hello-kubernetes-cluster  us-east1-b  1.8.8-gke.0     35.229.110.241  f1-micro      1.8.8-gke.0   3          RUNNING
```

* Now, connect `kubectl` client to the cluster by running:

```
gcloud container clusters get-credentials hello-kubernetes-cluster --zone us-east1-b
```

* Now...
    * The local system can interact with...
        * Google Cloud Platform
        * Docker
        * Kubernetes
    * There's an app developed in node
    * That app has been made into an operational docker image
    * A cluster has been created
* We're well on our way


Put the Docker Image into Google's Container Image Registry
-----------------------------------------------------------

* Google has a its container image registry for its cloud platform.
* It's there to make it easier to automate image updates, and distribution throughout the cluster.
* To put an image there, it has to be built with a proper name.
* To build the image of this app, and tag it for pushing into the image registry:

```
# docker build -t gcr.io/{PROJECT_ID}/hello-kubernetes-image:v1 {SRC_DIRECTORY}
sudo docker build -t gcr.io/hello-kubernetes-200203/hello-kubernetes-image:v1 .
```

* It's roughly the same `docker build` from before, *except*, it's important to include the `gcr.io`, the project id, and the name of the image, appended by a version
* Then, upload the image using:

```
gcloud docker -- push gcr.io/hello-kubernetes-200203/hello-kubernetes-image:v1
```

* The image gave me problems, because of permissions issues
    *   The best solution I've found so far is this stack overflow [response][06]
    * This should be solved with this command: `sudo usermod -a -G docker ${USER}`




References
==========

1. [Google Cloud: SDK Documentation][01]
2. [Google Cloud: Project Documentation][02]
3. [Google Cloud: Console][03]
4. [Github: jakubroztocil/httpie - A CLI HTTP Client][04]
5. [Wikipedia: Hard Link][05]
6. [StackOverflow: Permissions Issues When Uploading Docker Image to GCR][06]

[01]: https://cloud.google.com/sdk/
[02]: https://cloud.google.com/resource-manager/docs/creating-managing-projects#creating_a_project
[03]: https://console.cloud.google.com
[04]: https://github.com/jakubroztocil/httpie
[05]: https://en.wikipedia.org/wiki/Hard_link
[06]: https://stackoverflow.com/questions/42224136/permission-denied-when-pulling-docker-image-from-google-cloud-container-registry#42224137

[i01]: ./images/gcp-dash-projectid.png

