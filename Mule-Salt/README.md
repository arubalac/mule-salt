Easy Mule and Salt testing with Docker
===========

This repo provides a mule with salt setup with one master and one (or multiple) minions to be able to test and work with states, pillars and all other salt functionality. By using docker it's easy to reproduce and test you code.

Image includes both mule, salt-master, salt-minion, salt-api and also salt-cloud to be able to test and troubleshoot all things salt. `SALT_USE` environment variable is used to determent if container should be running as master or minion.

Salt master is auto accepting all minions.

## Salt versions

### Supported tags and respective `Dockerfile` links

 - **latest** Picks the latest by default

## Get it running

### Mule-Salt master/minon with docker run

Run one container with a master/minion setup. Below docker command for running master

```
docker run -i -t --name=mulesaltdocker_master_1 -h master -p 4505 -p 4506 \
   -p 8080 -p 8081 -e SALT_NAME=master -e SALT_USE=master \
   -v `pwd`/srv/salt:/srv/salt:rw cisco/mule-salt
```
Below docker command for running minion.

```
docker run -i -t --name=mulesaltdocker_minion_1 -h minion1 -p 4505 -p 4506 \
   -p 8080 -p 8081 -e SALT_NAME=minion1 -e SALT_USE=minion \
   -v `pwd`/srv/salt:/srv/salt:rw cisco/mule-salt
```

## Note:

The current version of mule-salt docker has limitation of dynamic ip detection. So 
you may need to get the docker ip of the master and set it in the minion file before running the minion.


By jumping in with `docker exec -i -t mulesaltdocker_master_1 bash` your able to test/troubleshoot. Now your ready to write you states and test them out.

## Environment variables

Env variables are used to set config on startup, you can set the following envs

 - `SALT_USE`  - master/minion, defaults to master
 - `SALT_NAME` - minion name, defaults to to master
 - `SALT_GRAINS` - set minion grains as json, defaults to none
 - `LOG_LEVEL` - log level, defaults to info
 - `OPTIONS`   - other options passed into salt process, defaults to none

## Volumes

Following paths can be mounted from the container. `/srv/salt` is needed to run your local states.

 - `/etc/salt` - Master/Minion config
 - `/var/cache/salt` - job data cache
 - `/var/logs/salt` - logs
 - `/srv/salt` - states, pillar reactors

## Build

### Use the pre built image
The pre built image can be downloaded using docker directly. After that you do not need to use this command again, you will have the image on your local computer.

```
docker pull cisco/mule-salt
```

### Build the docker image by yourself
If you prefer you can easily build the docker image by yourself. After this the image is ready for use on your machine and can be used for multiple starts.

```
git clone https://github.com/arubalac/mule-salt.git
cd salt-docker
docker build -t cisco/mule-salt .
```
