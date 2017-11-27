
# How to get your Mac to talk to docker on QNAP server

To get your Mac to talk to the QNAP server we can use docker-machine
to manage that connection.  That can be very useful if you are already using
docker machine.

First create the new link with docker machine - and it will be named qnap:
```bash
docker-machine create --driver=none --url tcp://<your qnap box>:2376 qnap
```

Unfortunately, docker-machine does not provide a way to set up the certificates
when using the --none option.  So we need to do that by hand.

Download certs from within the QNAP Container Station in the preferences section.
We will then place them in qnap directory created by docker-machine above:

```bash
cd ~/.docker/machine/machines/qnap
cp ~/Downloads/cert/*.pem .
```

You then need to edit the config.json file to be somethng like this (obviously your paths will vary):
```json
        "AuthOptions": {
            "CertDir": "/Users/rayj/.docker/machine/machines/qnap/",
            "CaCertPath": "/Users/rayj/.docker/machine/machines/qnap/ca.pem",
            "CaPrivateKeyPath": "/Users/rayj/.docker/machine/machines/qnap/key.pem",
            "CaCertRemotePath": "",
            "ServerCertPath": "/Users/rayj/.docker/machine/machines/qnap/cert.pem",
            "ServerKeyPath": "/Users/rayj/.docker/machine/machines/qnap/key.pem",
            "ClientKeyPath": "/Users/rayj/.docker/machine/machines/qnap/key.pem",
            "ServerCertRemotePath": "",
            "ServerKeyRemotePath": "",
            "ClientCertPath": "/Users/rayj/.docker/machine/machines/qnap/cert.pem",
            "ServerCertSANs": [],
            "StorePath": "/Users/rayj/.docker/machine/machines/qnap"
        }
```

You can test by doing something like this:
```bash
$> docker-machine ls --filter name=qnap
```

Then to use this machine do:
```bash
$> eval `docker-machine env qnap`
```

Then do normal docker commands like:
```bash
$> docker ps
```

You may have a newer version of docker running on your developer machine.  If that happens
you will get a message something like this:
**Error response from daemon: client is newer than server (client API version: 1.24, server API version: 1.23)**

You can set an environment variable to make Docker happy:
export DOCKER_API_VERSION=1.23

(If using docker-compose you may also want to set COMPOSE_API_VERSION as well.)

It is really a shame docker-machine can not set the DOCKER_API_VERSION for us!

### Problem with Docker for Mac

Unfortunately, it appears that Docker for Mac version 17.09.0-ce has a bug and it does not
honor the DOCKER_API_VERSION environment variable.

I was able to work around this by using docker version manager (dvm):
https://howtowhale.github.io/dvm/

You can install this on a Mac using brew:
```bash
brew install dvm
dvm use 17.05.0-ce
```

I use version 17.05.0-ce of docker which properly honors DOCKER_API_VERSION


After all that you should be able use your docker client to talk to the engine on your qnap
server just fine!

### Bash functions to make life easier

Unfortunately, things are still a little cumbersome - you have to call docker-machine, set
one or two environment variables, and call dvm.  Ug!  To make my life a little simpler I added the following to my .bash_profile

```bash
function setTerm() { PROFILE=${1}; echo "tell app \"Terminal\" to set current settings of first window to settings set \"${PROFILE}\""|osascript; };

setQnap() {
  eval $(docker-machine env qnap)
  export DOCKER_API_VERSION=1.23
  export COMPOSE_API_VERSION=1.23
  dvm use 17.05.0-ce
  setTerm homebrew
}

unsetQnap() {
  eval $(docker-machine env -u)
  unset DOCKER_API_VERSION
  unset COMPOSE_API_VERSION
  dvm deactivate
  setTerm basic
}
```

Then I just call setQnap or unsetQnap to switch between talking to the qnap docker
engine or the one om my laptop.  (The setTerm stuff is specific to Mac terminal and can
be removed.)

