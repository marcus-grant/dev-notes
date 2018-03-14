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
* 




## References
1. [gcloud - Google Cloud Shell Command Line Tool][01]
2. [Google Cloud SDK Downloads][02]

[01]: https://cloud.google.com/sdk/gcloud/ "gcloud - Google Cloud Shell Command Line Tool"
[02]: https://cloud.google.com/sdk/downloads#versioned "Google Cloud SDK Downloads"

