# Mosquitto

[Mosquitto](https://mosquitto.org) is an open source message broker often used in the world of IOT.

I wanted to play with it for some home-automation stuff.  Again - make sure you have done the [Docker Setup](../DockerSetup.md).  The file **docker-compose.yml** will easily get you going.

This compose file created a named volume called *mosquitto_data*.  You will want to ssh into your QNAP box to adjust some of the settings.

```bash
vi /share/Container/container-station-data/lib/docker/volumes/mosquitto_data/_data/config/mosquitto.conf
```

Check out the man page for how to configure mosquitto [here](http://mosquitto.org/man/mosquitto-conf-5.html).  Here is a smple to get you going.

```ini
# mqtt configuration file
# See http://mosquitto.org/man/mosquitto-conf-5.html
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
```


### Security

You really should lock the thing down at least a little.  You can add a basic password file to help a little.  Though you really should set up TLS to ensure real security.

You may not have the utility command-line tools for Mosquitto installed.  but don't worry they are in the docker container you are using!  The following command will create a new password file.

```bash
# Create a password file
docker run --rm -it -w /mosquitto/config -v mosquitto_data:/mosquitto eclipse-mosquitto mosquitto_passwd -c passwords.txt <username>
```

When you run this it then requires you to enter the password via stdin twice.  However, there was something weird running from docker because the prompt for the password came after I hit enter!

As you add more it is a little easier to use the -b (batch mode) where you can pass the username and password on the command line.

```bash
docker run --rm -it -w /mosquitto/config -v mosquitto_data:/mosquitto eclipse-mosquitto mosquitto_passwd -b passwords.txt bobby bobbypassword
```